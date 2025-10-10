## 2.3 동적 2D 배열과 다차원 배열 관리
* 정적인 2차원 배열(int arr[3][4])은 크기가 고정되어 있지만,
* 동적 2D 배열은 런타임에 메모리를 할당하고 유연하게 사용할 수 있습니다.

* C에서 동적 2D 배열을 만드는 방법은 크게 3가지입니다:

| 방법                | 설명                                           |
| ----------------- | -------------------------------------------- |
| 1. 포인터 배열         | `int* arr[n]` 형태로 행마다 별도 메모리 할당              |
| 2. 이중 포인터         | `int** arr`을 사용해 전체 구조를 완전히 동적 생성            |
| 3. 1D 배열을 2D처럼 접근 | `int* arr = malloc(row * col * sizeof(int))` |

### 2.3.1 포인터 배열
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

### 2.3.2 이중 포인터
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

### 2.3.3  1차원 배열로 다차원 배열처럼 사용

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
