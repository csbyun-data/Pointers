## 1.4 이중 포인터 이해  
이중 포인터(Double Pointer)는 포인터를 가리키는 포인터입니다.
즉, 포인터의 주소를 저장하는 포인터입니다.
```c
int **pp;
```
이 경우, pp는 int * 타입의 포인터를 가리키는 포인터입니다.  
따라서 int 값에 도달하려면 **pp 와 같이 두 번 간접 참조를 해야 합니다.

### 1.4.1 왜 이중 포인터가 필요할까?
이중 포인터는 다음과 같은 상황에서 유용합니다:

| 상황                     | 설명                                   |
| ---------------------- | ------------------------------------ |
| 함수에서 포인터 값을 변경하고 싶을 때  | 예: `malloc`된 메모리를 함수에서 할당            |
| 포인터 배열을 처리할 때          | 예: `char *argv[]` 는 사실 `char **argv` |
| 2차원 배열처럼 포인터 배열을 구성할 때 | 예: `int **matrix`                    |
```c
#include <stdio.h>

int main(int argc, char **argv) {
    printf("프로그램 이름: %s\n", argv[0]);

    for (int i = 1; i < argc; i++) {
        printf("인자 %d: %s\n", i, argv[i]);
    }

    return 0;
}
```
📌 이 코드는 실제 char *argv[] 가 char **argv 로 해석된다는 걸 실습으로 보여줍니다.

예제 1: 기본 개념 이해
```c
#include <stdio.h>

int main() {
  int num = 10;
  int *p = &num;
  int **pp = &p;

  +--------+       +--------+       +--------+
  |   pp   | --->  |   p    | --->  |  num   |
  +--------+       +--------+       +--------+
      주소              주소            값: 10
  printf("num: %d\n", num);
  printf("*p: %d\n", *p);
  printf("**pp: %d\n", **pp);

  return 0;
}
```
✔ 실행 결과:
```less
num: 10
*p: 10
**pp: 10
```

해설:
| 변수     | 의미                |
| ------ | ----------------- |
| `p`    | `num`의 주소를 저장     |
| `pp`   | `p`의 주소를 저장       |
| `*p`   | `num`의 값 (10)     |
| `**pp` | `*p` → `num` → 10 |

예제 2: 함수에서 포인터 값 변경하기
```c
#include <stdio.h>
#include <stdlib.h>

void allocate(int **ptr) {
  *ptr = (int *)malloc(sizeof(int));
  **ptr = 42;
}

int main() {
  int *p = NULL;
  allocate(&p);

  printf("할당된 값: %d\n", *p);

  if (p != NULL)
    free(p);  // 메모리 해제
  return 0;
}
```
📌 함수에서 이중 포인터를 통해 포인터에 메모리를 할당하고 값을 설정합니다.

예제 3: 포인터 배열
```c
#include <stdio.h>

int main() {
  const char *fruits[] = {"Apple", "Banana", "Cherry"};
  const char **pp = fruits;

  for (int i = 0; i < 3; i++) {
    printf("%s\n", *(pp + i));  // 또는 pp[i]
  }

  return 0;
}
```
설명:
* fruits는 문자열을 가리키는 포인터들의 배열 → char *[3]
* pp는 이 배열을 가리키는 이중 포인터 → char **

예제 4: 2차원 동적 배열 만들기
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int rows = 2, cols = 3;

    int **matrix = (int **)malloc(rows * sizeof(int *));
    if (matrix == NULL) {
        printf("matrix 메모리 할당 실패\n");
        return 1;
    }
    for (int i = 0; i < rows; i++) {
        matrix[i] = (int *)malloc(cols * sizeof(int));
        if (matrix[i] == NULL) {
            printf("matrix[%d] 메모리 할당 실패\n", i);
            return 1;
        }
    }

    // 값 저장
    for (int i = 0; i < rows; i++)
        for (int j = 0; j < cols; j++)
            matrix[i][j] = i * cols + j;

    // 출력
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            printf("%d ", matrix[i][j]);
        }
        printf("\n");
    }

    // 메모리 해제
    for (int i = 0; i < rows; i++) {
      if (matrix[i] != NULL)
        free(matrix[i]);
    }
    if (matrix != NULL)
      free(matrix);

    return 0;
}
```

⚠️ 주의 사항
| 실수                                                  | 설명                  |
| --------------------------------------------------- | ------------------- |
| `*ptr = malloc()` → `ptr`이 포인터 주소를 가리키지 않으면 세그폴트 발생 |                     |
| 포인터의 포인터 연산 혼동                                      | `*p`와 `**pp` 구분 필요  |
| 동적 배열 메모리 해제 누락                                     | 각 행과 최종 배열 모두 해제 필요 |

### 1.4.2 포인터 배열 vs 이중 포인터 요약 비교표
```c
// 포인터 배열: 여러 포인터 저장하는 배열
char *arr1[3] = {"one", "two", "three"};

// 이중 포인터: 포인터 하나를 가리키는 포인터
char **ptr = arr1;
```
| 구분     | 설명                      | 예시                            |
| ------ | ----------------------- | ----------------------------- |
| 포인터 배열 | 포인터들의 배열                | `char *arr[3]`                |
| 이중 포인터 | 포인터를 가리키는 포인터           | `char **pp = arr`             |
| 공통점    | 둘 다 `char **`로 취급될 수 있음 | `main(int argc, char **argv)` |
| 차이점    | 메모리 구조에서 배열 vs 단일 포인터   | 다차원 처리 방식 차이                  |


✅ 연습 문제

1. 다음 코드의 출력 결과는?
```c
int a = 100;
int *p = &a;
int **pp = &p;

printf("%p\n", pp);
printf("%p\n", *pp);
printf("%d\n", **pp);
```
📌 주의: %p는 주소 출력, %d는 값 출력

2. 이중 포인터를 사용해 1차원 동적 배열을 생성하고 5개의 정수를 저장한 후 출력하세요.
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *arr = NULL;
    int **pp = &arr;

    *pp = (int *)malloc(5 * sizeof(int));
    if (*pp == NULL) {
        printf("메모리 할당 실패\n");
        return 1;
    }

    for (int i = 0; i < 5; i++) {
        (*pp)[i] = i * 10;
    }

    for (int i = 0; i < 5; i++) {
        printf("%d ", arr[i]);  // 또는 (*pp)[i]
    }
    printf("\n");

    free(arr);
    return 0;
}
```
3. 포인터 배열과 이중 포인터의 차이점을 비교해보세요.

| 개념               | 설명                          | 예시                              |
| ---------------- | --------------------------- | ------------------------------- |
| 이중 포인터           | 포인터를 가리키는 포인터               | `int **pp = &p;`                |
| 간접 참조            | `**pp`는 원래 데이터에 접근          | `**pp = 10;`                    |
| 사용 예             | malloc, 포인터 변경, 다차원 배열 등    | `allocate(&p);`, `matrix[i][j]` |
| 포인터 배열 vs 이중 포인터 | 배열에 여러 포인터 vs 포인터를 가리키는 포인터 | `char *arr[]` vs `char **pp`    |

💡 요약
> * 이중 포인터는 포인터의 주소를 저장함.
> * 함수에서 포인터 값을 변경할 때 유용.
> * 다차원 동적 배열을 만들기 위한 필수 개념.
> * 문자열 배열이나 argv 처리 시에도 사용.
