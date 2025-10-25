## 1.4 이중 포인터 이해  
이중 포인터(Double Pointer)는 포인터를 가리키는 포인터입니다.
즉, 포인터의 주소를 저장하는 포인터입니다.
| 개념     | 설명                          |
| ------ | --------------------------- |
| 이중 포인터 | 포인터를 가리키는 포인터 (`int **pp`)  |
| 참조 깊이  | `**pp` → `*p` → `변수값`       |
| 메모리 구조 | 변수 → 포인터 → 이중 포인터 순으로 주소 참조 |

```c
int num = 10;
int *p = &num;
int **pp = &p;
```

### 1.4.1 왜 이중 포인터가 필요할까?
이중 포인터는 다음과 같은 상황에서 유용합니다:

| 사용 상황            | 설명                                                  |
| ---------------- | --------------------------------------------------- |
| 함수에서 포인터 값 변경  | 이중 포인터를 사용하면 함수 내에서 포인터 자체를 수정 가능                   |
| 동적 2차원 배열 처리   | `int **matrix` 방식으로 행 단위 메모리 할당                     |
| 포인터 배열 처리      | 문자열 배열, 명령행 인자 처리 (`char *argv[]` == `char **argv`) |
| 다차원 배열 및 구조 처리 | 다차원 구조체/배열 등을 동적으로 구성 가능                            |

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

예제 2: 단일 포인터 전달 (주소 변경되지 않음)
* 포인터는 값에 의한 전달(pass by value) 이므로, 함수 내에서 포인터 자체를 바꾸려면 이중 포인터를 사용해야 합니다.
```c
void wrong_alloc(int *p) {
  p = malloc(sizeof(int)); // 지역 변수 p만 변경됨
  *p = 5;
}

int main() {
  int *ptr = NULL;
  wrong_alloc(ptr); // ptr은 여전히 NULL
  printf("%p\n", ptr); // NULL 출력
}
```

예제 3: 함수에서 포인터 값 변경하기
```c
#include <stdio.h>
#include <stdlib.h>

void correct_alloc(int **ptr) {
  *ptr = (int *)malloc(sizeof(int));
  **ptr = 42;
}

int main() {
  int *p = NULL;
  correct_alloc(&p);

  printf("할당된 값: %d\n", *p);

  if (p != NULL)
    free(p);  // 메모리 해제
  return 0;
}
```
📌 함수에서 이중 포인터를 통해 포인터에 메모리를 할당하고 값을 설정합니다.

예제 4: 문자열 포인터 배열 (argv 구조)
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

예제 5: 동적 2차원 배열 생성 및 해제
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
| 실수 예시                                 | 설명                         |
| ------------------------------------- | -------------------------- |
| `*ptr = malloc(...)` 사용 시 `ptr`이 NULL | 함수 호출 시 이중 포인터로 주소를 넘겨야 안전 |
| 메모리 누수                                | `malloc`한 행마다 `free` 필요    |
| 간접 참조 오용 (`*p` vs `**pp`)             | 포인터 대상과 주소 대상 혼동 주의        |


### 1.4.2 포인터 배열 vs 이중 포인터 요약 비교표
```c
// 포인터 배열: 여러 포인터 저장하는 배열
char *arr1[3] = {"one", "two", "three"};

// 이중 포인터: 포인터 하나를 가리키는 포인터
char **ptr = arr1;
```
📌 비교: 포인터 배열 vs 이중 포인터
| 구분        | 포인터 배열 (`char *arr[3]`)  | 이중 포인터 (`char **pp`)       |
| --------- | ------------------------ | -------------------------- |
| 구조        | 여러 포인터를 원소로 가지는 배열       | 포인터 하나를 가리키는 포인터           |
| 메모리 할당 방식 | 배열 자체는 스택, 문자열은 힙        | 가리키는 대상이 다양 (배열, 동적 할당 등)  |
| 주요 사용처    | 문자열 리스트, 테이블, argv 등     | 함수 인자 전달, 2차원 배열, 포인터 수정 등 |
| 예시        | `char *fruits[] = {...}` | `char **pp = fruits;`      |

### 1.4.3 동적 2차원 배열(이중 포인터 활용 패턴)
```c
int **make_matrix(int rows, int cols) {
    int **m = malloc(rows * sizeof(int *));
    for (int i = 0; i < rows; i++)
        m[i] = malloc(cols * sizeof(int));
    return m;
}
```
* int (*m)[cols] 방식(고정 크기 2차원 배열 포인터)과는 다릅니다.
  위 예제는 “각 행이 따로 동적 할당된 독립 블록”입니다.
  
✅ 연습 문제

1. 다음 코드의 출력 결과는?
```c
int a = 100;
int *p = &a;
int **pp = &p;

printf("%p\n", pp);    // pp의 주소
printf("%p\n", *pp);   // p의 주소 = &a
printf("%d\n", **pp);  // 100
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

| 항목     | 포인터 배열                   | 이중 포인터              |
| ------ | ------------------------ | ------------------- |
| 구조     | 포인터들의 배열                 | 포인터를 가리키는 포인터       |
| 선언 방식  | `char *arr[3];`          | `char **pp;`        |
| 메모리 모델 | 스택 배열 (포인터는 힙을 가리킬 수 있음) | 포인터 하나를 가리키는 구조     |
| 사용 예   | argv, 문자열 테이블 등          | malloc 함수, 포인터 변경 등 |


💡 요약
> * 이중 포인터 (int **pp): 포인터를 가리키는 포인터
> * 포인터 자체를 수정하고 싶을 때 (함수 내 포인터 수정)
> * 포인터 배열 처리
> * 문자열 배열이나 argv 처리 시에도 사용.
