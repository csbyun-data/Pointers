## 1.7 포인터와 배열

배열과 포인터는 C 언어에서 밀접한 관계를 갖고 있으며, 많은 경우에 배열 이름이 포인터처럼 동작합니다. 하지만 배열과 포인터는 동일하지 않으며, 구체적인 차이와 사용법을 이해해야 합니다.

### 1.7.1 1차원 배열과 포인터  
* 배열의 이름은 해당 배열의 첫 번째 요소의 주소를 나타내는 상수 포인터입니다.
* 배열을 통해서든 포인터를 통해서든 동일한 방식으로 요소에 접근할 수 있습니다.

예제 1:
```c
#include <stdio.h>

int main() {
    int arr[5] = {10, 20, 30, 40, 50};
    int *ptr = arr;

    printf("arr[0] = %d\n", arr[0]);     // 배열을 이용한 접근
    printf("*ptr = %d\n", *ptr);         // 포인터를 이용한 접근
    printf("*(arr + 2) = %d\n", *(arr + 2)); // 포인터 연산
    printf("ptr[3] = %d\n", ptr[3]);     // 포인터도 배열처럼 사용 가능

    return 0;
}
```
* 포인터 요약
| 표현           | 의미                       | 예시 값 (`arr[5] = {10,20,30,...}`) |
| ------------ | ------------------------ | -------------------------------- |
| `arr`        | `&arr[0]`과 동일 (배열 시작 주소) | `arr == &arr[0]`                 |
| `arr[i]`     | i번째 요소                   | `arr[2] == 30`                   |
| `*(arr + i)` | i번째 요소 (포인터 방식 접근)       | `*(arr + 2) == 30`               |
| `ptr[i]`     | 포인터를 배열처럼 사용             | `ptr = arr; ptr[3] == 40`        |
| `*(ptr + i)` | 포인터 연산                   | `*(ptr + 4) == 50`               |

📌 배열 이름은 상수 포인터처럼 동작하지만, 재할당은 불가능함 (arr = ptr; ❌)

### 1.7.2 2차원 배열과 포인터  
* 2차원 배열은 배열의 배열입니다.
* 포인터를 이용해 2차원 배열의 요소에 접근할 수 있지만, 포인터의 타입을 정확히 이해해야 합니다.
```c
int arr[2][3] = {{1,2,3},{4,5,6}};
```

| 표현                  | 의미                 | 결과                       |
| ------------------- | ------------------ | ------------------------ |
| `arr[i][j]`         | i행 j열의 요소          | `arr[1][2] == 6`         |
| `*(*(arr + i) + j)` | 포인터 연산으로 동일한 요소 접근 | `*(*(arr + 1) + 2) == 6` |

예제 2: 2차원 배열과 포인터 접근
```c
#include <stdio.h>

int main() {
    int arr[2][3] = {{1, 2, 3}, {4, 5, 6}};

    printf("arr[1][2] = %d\n", arr[1][2]);           // 6
    printf("*(*(arr + 1) + 2) = %d\n", *(*(arr + 1) + 2)); // 6

    return 0;
}
```

배열 포인터의 선언
```c
int (*ptr)[3]; // '3개의 int를 가진 배열'을 가리키는 포인터
```
```c
int arr[2][3];
ptr = arr; // OK
```
📌 ptr + 1 → 다음 행으로 이동 (int[3] 크기만큼)

### 1.7.3 배열 포인터 vs 포인터 배열
| 구분     | 선언             | 설명                              |
| ------ | -------------- | ------------------------------- |
| 배열 포인터 | `int (*p)[3];` | 정수 3개짜리 배열 하나를 가리킴 (`배열 1개`)    |
| 포인터 배열 | `int *p[3];`   | 정수 포인터 3개를 원소로 갖는 배열 (`포인터 3개`) |

* 배열 포인터 (Pointer to Array)
```c
int (*p)[3]; // 3개의 int를 가진 배열을 가리키는 포인터
```
* 포인터 배열 (Array of Pointers)
```c
int *p[3]; // int형 포인터 3개를 원소로 가진 배열
```
예제 3:
```c
#include <stdio.h>

int main() {
    int a = 10, b = 20, c = 30;
    int *p_arr[3] = {&a, &b, &c}; // 포인터 배열

    for (int i = 0; i < 3; i++) {
        printf("p_arr[%d] = %d\n", i, *p_arr[i]);
    }

    return 0;
}
```
📌 예제 정리
배열을 포인터로 순회
```c
int arr[] = {1, 2, 3, 4, 5};
int *p = arr;

for (int i = 0; i < 5; i++) {
    printf("%d ", *(p + i));
}

2차원 배열을 함수에 전달
void print2D(int arr[][3], int row) {
    for (int i = 0; i < row; i++) {
        for (int j = 0; j < 3; j++) {
            printf("%d ", arr[i][j]);
        }
        printf("\n");
    }
}

int main() {
    int data[2][3] = {{1, 2, 3}, {4, 5, 6}};
    print2D(data, 2);
    return 0;
}
```
### 1.7.4 동적 2D 배열과 다차원 배열 관리
* 정적인 2차원 배열(int arr[3][4])은 크기가 고정되어 있지만,
* 동적 2D 배열은 런타임에 메모리를 할당하고 유연하게 사용할 수 있습니다.

* C에서 동적 2D 배열을 만드는 방법은 크게 3가지입니다:

| 방법                | 설명                                           |
| ----------------- | -------------------------------------------- |
| 1. 포인터 배열         | `int* arr[n]` 형태로 행마다 별도 메모리 할당              |
| 2. 이중 포인터         | `int** arr`을 사용해 전체 구조를 완전히 동적 생성            |
| 3. 1D 배열을 2D처럼 접근 | `int* arr = malloc(row * col * sizeof(int))` |

#### 포인터 배열
예제 1: 포인터 배열 (행 고정, 열 가변 가능)
```C
#include <stdio.h>
#include <stdlib.h>

int main() {
  int rows = 3, cols = 4;
  int* arr[3]; // 정적 포인터 배열
  
  // 각 행에 동적 할당
  for (int i = 0; i < rows; i++) {
    arr[i] = (int*)malloc(cols * sizeof(int));
  }
  
  // 값 할당 및 출력
  for (int i = 0; i < rows; i++) {
    for (int j = 0; j < cols; j++) {
        arr[i][j] = i * cols + j;
        printf("%2d ", arr[i][j]);
    }
    printf("\n");
  }
  
  // 메모리 해제
  for (int i = 0; i < rows; i++) {
    free(arr[i]);
  }
  
  return 0;
}
```

#### 이중 포인터
예제 2: 이중 포인터 (int**)
```C
#include <stdio.h>
#include <stdlib.h>

int main() {
  int rows = 3, cols = 4;
  int** arr;

  // 행 포인터 배열 할당
  arr = (int**)malloc(rows * sizeof(int*));

  // 각 행에 열 할당
  for (int i = 0; i < rows; i++) {
    arr[i] = (int*)malloc(cols * sizeof(int));
  }

  // 값 할당 및 출력
  for (int i = 0; i < rows; i++) {
    for (int j = 0; j < cols; j++) {
      arr[i][j] = i + j;
      printf("%2d ", arr[i][j]);
    }
    printf("\n");
  }

  // 메모리 해제
  for (int i = 0; i < rows; i++) {
    free(arr[i]);
  }
  free(arr);

  return 0;
}
```

#### 1차원 배열로 다차원 배열처럼 사용

예제 3: 1차원 배열로 다차원 배열처럼 사용
```C
#include <stdio.h>
#include <stdlib.h>

int main() {
  int rows = 3, cols = 4;
  int* arr = (int*)malloc(rows * cols * sizeof(int));

  // 값 할당 및 출력 (2D처럼 접근)
  for (int i = 0; i < rows; i++) {
    for (int j = 0; j < cols; j++) {
      *(arr + i * cols + j) = i * cols + j;
      printf("%2d ", *(arr + i * cols + j));
    }
    printf("\n");
  }

  free(arr);
  return 0;
}
```

* 장점: 메모리 연속성 ↑, 캐시 효율 ↑
* 단점: 가독성 ↓, 인덱싱 수동 처리 필요

* 언제 어떤 방식 써야 할까?
 
| 방식     | 장점               | 단점                |
| ------ | ---------------- | ----------------- |
| 포인터 배열 | 행마다 크기 다르게 설정 가능 | 행 수는 고정           |
| 이중 포인터 | 완전한 동적 생성        | 메모리 관리 복잡         |
| 1D 배열  | 연속된 메모리, 빠름      | 인덱싱 수동, 코드 가독성 낮음 |


✅ 연습 문제

1. 포인터를 사용하여 1차원 배열의 모든 요소를 출력하는 함수를 작성해보세요.
```c
#include <stdio.h>

void printArray(int *arr, int size) {
  for (int i = 0; i < size; i++) {
    printf("%d ", *(arr + i));  // 또는 arr[i]
  }
  printf("\n");
}

int main() {
  int data[5] = {10, 20, 30, 40, 50};
  printArray(data, 5);
  return 0;
}
```
2. 포인터를 이용하여 2차원 배열의 합을 구하는 코드를 작성해보세요.
```c
#include <stdio.h>

int sum2D(int (*arr)[3], int row) {
  int sum = 0;
  for (int i = 0; i < row; i++) {
    for (int j = 0; j < 3; j++) {
      sum += *(*(arr + i) + j);  // 또는 arr[i][j]
    }
  }
  return sum;
}

int main() {
  int mat[2][3] = {{1, 2, 3}, {4, 5, 6}};
  printf("합계: %d\n", sum2D(mat, 2));  // 출력: 21
  return 0;
}
```
3. 배열 포인터와 포인터 배열의 차이를 설명하고 각각 예제를 만들어보세요.
* 포인터 배열 (int *p[3])
```c
#include <stdio.h>

int main() {
  int a = 1, b = 2, c = 3;
  int *p[3] = {&a, &b, &c};  // 포인터 배열

  for (int i = 0; i < 3; i++) {
    printf("%d ", *p[i]);  // 출력: 1 2 3
  }
  return 0;
}
```
* 배열 포인터 (int (*p)[3])
```c
#include <stdio.h>

int main() {
  int arr[2][3] = {{1, 2, 3}, {4, 5, 6}};
  int (*p)[3] = arr;  // 배열 포인터

  for (int i = 0; i < 2; i++) {
    for (int j = 0; j < 3; j++) {
      printf("%d ", p[i][j]);  // 출력: 1 2 3 4 5 6
    }
  }
  return 0;
}
```
* 차이 요약
| 구분            | 메모리 구조         | 용도                     |
| ------------- | -------------- | ---------------------- |
| `int *p[3]`   | 포인터 3개 (각각 따로) | 여러 변수의 주소 저장           |
| `int (*p)[3]` | 배열 한 덩어리를 가리킴  | 2차원 배열, 배열 매개변수로 자주 사용 |

📚 요약
| 내용           | 요점 정리                                       |
| ------------ | ------------------------------------------- |
| 배열 이름 `arr`  | 배열의 시작 주소 (상수 포인터)                          |
| 포인터 접근       | `*(arr + i)`, `ptr[i]` 등 가능                 |
| 배열 포인터       | `int (*p)[N]` → 배열 전체를 가리킴                  |
| 포인터 배열       | `int *p[N]` → 포인터 여러 개 저장                   |
| 2차원 배열 함수 전달 | `void f(int (*a)[COLS])` or `int a[][COLS]` |

