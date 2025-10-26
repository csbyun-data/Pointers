## 1.10 메모리 할당 실수 방지와 안전한 포인터 사용
* 동적 메모리를 사용할 때는 반드시 주의가 필요합니다. 이 장에서는 C 언어에서 발생하기 쉬운 메모리 관련 실수를 정리하고, 이를 방지하는 실천 방법과 예제를 소개합니다.

### 1.10.1 Null 포인터 점검  
* 메모리 할당이 실패했을 때 malloc()은 NULL을 반환합니다. 이 포인터를 사용하면 Segmentation Fault가 발생할 수 있습니다.
✅ 안전한 코드
```c
int *ptr = (int *)malloc(sizeof(int) * 5);
if (ptr == NULL) {
  fprintf(stderr, "메모리 할당 실패\n");
  exit(1);
}
```
### 1.10.2 할당 후 초기화 누락  
* 동적 메모리를 할당받은 후 값을 초기화하지 않으면, **쓰레기 값(garbage value)** 가 포함되어 있을 수 있습니다.

✅ 안전한 초기화 방법
```c
// 방법 1: 수동 초기화
for (int i = 0; i < 5; i++) arr[i] = 0;

// 방법 2: calloc 사용
int *arr = (int *)calloc(5, sizeof(int));  // 자동 0 초기화
```

### 1.10.3 해제 후 포인터 사용 금지 (Dangling Pointer)  
* free()로 메모리를 해제한 후, 해당 포인터를 그대로 사용하면 dangling pointer 오류 발생.
✅ 해결 방법
```c
free(ptr);
ptr = NULL;  // 포인터 무효화 → 사용 시 바로 감지 가능
```
### 1.10.4 이중 해제 방지  
* free()를 두 번 호출하면 시스템이 예기치 않게 동작하거나 충돌할 수 있습니다.

❌ 잘못된 예
```c
free(ptr);
free(ptr);  // 두 번째 호출은 오류
```

✅ 안전한 방법
```c
free(ptr);
ptr = NULL;
```
### 1.10.5 메모리 누수 방지  
* malloc()으로 메모리를 할당했지만 free()하지 않으면 메모리 누수 발생.
✅ 일반적인 해결 방법

함수 종료 전 free() 호출

### 1.10.6 구조화된 자원 관리 방식(RAII, 스마트 포인터 등) 사용

예제 1: 초기화 누락과 초기화 된 메모리 비교
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
  int *a = (int *)malloc(5 * sizeof(int));
  int *b = (int *)calloc(5, sizeof(int));

  printf("malloc: ");
  for (int i = 0; i < 5; i++) printf("%d ", a[i]);  // 쓰레기 값

  printf("\ncalloc: ");
  for (int i = 0; i < 5; i++) printf("%d ", b[i]);  // 0

  free(a); a = NULL;
  free(b); b = NULL;
  return 0;
}
```

예제 2: Dangling Pointer 방지
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
  int *p = (int *)malloc(sizeof(int) * 3);
  p[0] = 100;

  free(p); p = NULL;
  // printf("%d", p[0]);  // ❌ dangling pointer 오류 발생 가능

  p = NULL;  // ✅ 안전하게 무효화
  return 0;
}
```

예제 3: 이중 해제 방지
```c
#include <stdio.h>
#include <stdlib.h>

void safe_free(int **ptr) {
  if (*ptr != NULL) {
    free(*ptr);
    *ptr = NULL;
  }
}

int main() {
  int *arr = (int *)malloc(sizeof(int) * 5);
  safe_free(&arr);
  safe_free(&arr);  // ✅ 이중 해제 방지

  return 0;
}
```

예제 4: 메모리 누수 예와 해결 방법
```c
// 메모리 누수 발생 예
int* create_array_bad() {
  int *arr = (int *)malloc(100 * sizeof(int));
  // free(arr); ❌ 안 하면 누수
  return arr;  // 사용 후 free()하지 않으면 누수
}

// 누수 해결
int* create_array_good() {
  int *arr = (int *)malloc(100 * sizeof(int));
  if (!arr) return NULL;
  return arr;
}

int main() {
  int *data = create_array_good();
  if (data) {
    // 사용
    free(data);  // ✅ 반드시 해제
  }
  return 0;
}
```

✅ 요약 정리표
| 실수 유형       | 증상/위험              | 방지법                  |
| ----------- | ------------------ | -------------------- |
| Null 포인터 사용 | Segmentation fault | `NULL` 체크 후 사용       |
| 초기화 누락      | 쓰레기 값 참조           | `calloc()` 또는 수동 초기화 |
| 해제 후 접근     | 예기치 않은 결과          | `free()` 후 `NULL` 할당 |
| 이중 해제       | 프로그램 충돌            | `safe_free()` 함수 사용  |
| 메모리 누수      | 점점 메모리 소비          | 사용 후 반드시 `free()`    |
