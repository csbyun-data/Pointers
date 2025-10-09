## 1.9 동적 메모리 할당과 해제
* C 언어는 정적 메모리(static memory) 외에도 동적 메모리(dynamic memory) 를 사용할 수 있도록 다양한 함수들을 제공합니다.
* 이 기능을 통해 실행 중 필요한 만큼 메모리를 확보하거나 해제할 수 있습니다.

### 1.9.1 malloc(), calloc(), realloc(), free 개요  
✅ malloc()
* 원하는 바이트 크기의 메모리 공간을 힙 영역에 할당.
* 반환 타입은 void*이며, 사용 시 형변환 필요.
```c
int *arr = (int *)malloc(sizeof(int) * 5);
```

✅ calloc()
* malloc과 유사하지만, 초기화를 0으로 자동 수행.
```c
int *arr = (int *)calloc(5, sizeof(int));  // 5개의 int 공간 0으로 초기화
```

✅ realloc()
* 기존에 할당한 메모리 공간의 크기를 확장하거나 축소.
* 기존 데이터를 유지하면서 새 공간으로 복사함.
```c
arr = (int *)realloc(arr, sizeof(int) * 10);
```

✅ free()
* malloc이나 calloc으로 할당한 메모리를 해제.
* free(arr);

🔎 메모리 할당 후 반드시 체크할 것!
```c
int *ptr = (int *)malloc(sizeof(int) * 10);
if (ptr == NULL) {
    printf("메모리 할당 실패\n");
    exit(1);
}
```

예제 1: N개의 정수를 입력받아 평균 계산하기
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
  int N;
  printf("정수의 개수 N 입력: ");
  scanf("%d", &N);
  
  int *arr = (int *)malloc(sizeof(int) * N);
  if (!arr) {
    printf("메모리 할당 실패\n");
    return 1;
  }
  
  printf("%d개의 정수를 입력하세요:\n", N);
  for (int i = 0; i < N; i++) {
    scanf("%d", &arr[i]);
  }
  
  int sum = 0;
  for (int i = 0; i < N; i++) {
    sum += arr[i];
  }
  
  double avg = (double)sum / N;
  printf("평균: %.2f\n", avg);
  
  free(arr);
  return 0;
}

```

예제 2: calloc을 사용하여 초기화 상태 확인
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
  int *arr = (int *)calloc(5, sizeof(int));
  if (!arr) return 1;

  for (int i = 0; i < 5; i++) {
      printf("%d ", arr[i]);  // 모두 0으로 출력
  }

  free(arr);
  return 0;
}
```

예제 3: realloc을 사용하여 배열 확장
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
  int *arr = (int *)malloc(sizeof(int) * 3);
  if (!arr) return 1;

  for (int i = 0; i < 3; i++) arr[i] = i + 1;

  arr = (int *)realloc(arr, sizeof(int) * 5);
  if (!arr) return 1;

  arr[3] = 4;
  arr[4] = 5;

  for (int i = 0; i < 5; i++) {
    printf("%d ", arr[i]);
  }

  free(arr);
  return 0;
}
```

⚠️ 메모리 할당 시 주의사항
| 문제                  | 설명                                |
| ------------------- | --------------------------------- |
| 메모리 할당 후 NULL 체크 누락 | 시스템이 메모리 부족할 경우 할당 실패할 수 있음       |
| 해제 누락               | `free()` 호출을 잊으면 메모리 누수 발생        |
| 해제 후 포인터 사용         | 해제 후 사용은 `dangling pointer` 오류 발생 |
| 이중 해제               | 같은 포인터를 두 번 `free()` 하면 오류 발생     |
| 잘못된 크기 계산           | 구조체나 포인터 크기 혼동 주의                 |

### 1.9.2 메모리 누수 감지 프로그램 (Valgrind 사용 예시)
예제 4: 잘못된 메모리 할당
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *arr;
    // scanf("%d", arr);  // ❌ arr이 메모리 할당되지 않았기 때문에 오류 발생

    arr = (int *)malloc(sizeof(int));
    if (!arr) return 1;

    scanf("%d", arr);  // ✅ 올바른 할당 후 입력
    printf("입력한 값: %d\n", *arr);

    free(arr);
    return 0;
}
```

```c
#include <stdio.h>
#include <stdlib.h>

void leak_example() {
  int *data = (int *)malloc(sizeof(int) * 10);
  if (!data) return;

  for (int i = 0; i < 10; i++) {
    data[i] = i;
  }

  // 의도적 누수: free(data); 를 하지 않음
}

int main() {
  leak_example();
  printf("메모리 누수 예제 실행 완료\n");
  return 0;
}
```
* Valgrind 사용 방법 (Linux/macOS)
```bash
gcc -g leak.c -o leak
valgrind --leak-check=full ./leak
```
* Valgrind 결과 예시:
```php
==12345== 40 bytes in 1 blocks are definitely lost in loss record 1 of 1
==12345==    at 0x4C2BBAF: malloc (vg_replace_malloc.c:xxx)
==12345==    by 0x4011F6: leak_example (leak.c:5)
==12345==    by 0x40122A: main (leak.c:13)
```
* 수정 후
```
free(data);  // 누수 방지
```

📚 정리 요약
| 문제        | 사용 함수                        | 핵심 기능            | 주의사항                   |
| --------- | ---------------------------- | ---------------- | ---------------------- |
| N개 평균 계산  | `malloc`, `free`             | 입력 크기만큼 동적 배열 생성 | 평균 계산 시 `(double)` 사용  |
| 배열 확장 입력  | `realloc`                    | 기존 배열을 2배 확장     | 실패 시 기존 메모리 해제 필요      |
| 메모리 누수 점검 | `valgrind`, `malloc`, `free` | 누수 시각화 도구 활용     | 디버깅 필수, `free()` 누락 금지 |

✅ 연습 문제

1. 사용자로부터 정수 N을 입력받고, N개의 정수를 입력받아 평균을 계산하는 프로그램을 작성해보세요.

2. realloc을 이용해 5개의 정수를 입력받고, 배열을 10개로 확장해 다시 입력받아 전체 출력해보세요.

3. 메모리 누수를 감지하기 위한 프로그램을 작성해보고, valgrind와 같은 도구로 점검해보세요.
