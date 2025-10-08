## 1.5 포인터와 함수 매개변수 전달 
C 언어에서는 함수에 인자를 전달할 때 기본적으로 Call by Value (값에 의한 전달) 방식이 사용됩니다.  
하지만 포인터를 이용하면 Call by Reference (참조에 의한 전달) 효과를 얻을 수 있습니다.

📚 C 언어: Call by Reference vs Call by Pointer 요약  

| 구분 | Call by Value | Call by Reference(Call by Pointer) | Call by Reference |
|------|----------------|------------------|--------------------|
| C 언어 지원 | ✅ 지원 | ✅ 지원 | ❌ 직접 지원 안 함 |
| 전달 방식 | 값 복사 | 주소 전달 | 참조 전달 (C++에서만 가능) |
| 원본 변경 가능 | ❌ 불가능 | ✅ 가능 | ✅ 가능 |
| 문법 예시 | `int x;` | `int *px;` | `int &rx;` (C++) |

> ⚠️ C 언어는 **Call by Reference를 직접 지원하지 않으며**, 포인터를 사용하여 **참조처럼 동작**하게 만듭니다.  
> ⚠️ 주의요함! C 언어에서 Call by Pointer를 Call by Reference(포인터 이용)의 용어를 사용하며 설명 함

### 1.5.1 Call by Value (값에 의한 전달)
* 변수의 복사본이 함수에 전달됨.
* 함수 내부에서 값을 바꾸더라도 원래 변수에는 영향 없음.

예제 1: Call by Value
```c
#include <stdio.h>

void change(int x) {
  x = 100;
}

int main() {
  int num = 10;
  change(num);
  printf("num = %d\n", num); // 출력: 10 (변화 없음)
  return 0;
}
```
### 1.5.2 Call by Reference (Pointer이용)
* 변수의 주소를 함수에 전달 → 포인터를 통해 원래 변수의 값을 변경할 수 있음.
* C에서는 포인터를 이용해 Call by Reference처럼 동작시킴.

예제 2: Call by Reference (포인터 이용, Call by Pointer)
```c
#include <stdio.h>

void change(int *p) {
  *p = 100;
}

int main() {
  int num = 10;
  change(&num);
  printf("num = %d\n", num); // 출력: 100
  return 0;
}
```
🔁 비교 정리
| 구분                | 설명     | 변수 변경 여부   |
| ----------------- | ------ | ---------- |
| Call by Value     | 값만 전달  | ❌ 원본 변경 없음 |
| Call by Reference(Call by Pointer이용) | 주소를 전달 | ✅ 원본 변경 가능 |

예제 3: 두 수를 교환하는 함수  
> Call by Value 실패 예제
```c
#include <stdio.h>

void swap(int a, int b) {
  int temp = a;
  a = b;
  b = temp;
}

int main() {
  int x = 1, y = 2;
  swap(x, y);
  printf("x = %d, y = %d\n", x, y); // 출력: x = 1, y = 2
  return 0;
}
```
> Call by Reference(Pointer 이용) 성공 예제
```c
#include <stdio.h>

void swap(int *a, int *b) {
  int temp = *a;
  *a = *b;
  *b = temp;
}

int main() {
  int x = 1, y = 2;
  swap(&x, &y);
  printf("x = %d, y = %d\n", x, y); // 출력: x = 2, y = 1
  return 0;
}
```
예제 4: 함수에서 동적 메모리 할당
함수 내부에서 메모리를 할당하고 그 결과를 외부 변수에 저장하려면 이중 포인터가 필요합니다.
```c
#include <stdio.h>
#include <stdlib.h>

void allocateArray(int **arr, int size) {
  *arr = (int *)malloc(size * sizeof(int));
  if (arr == NULL) {
    printf("메모리 할당 실패\n");
    return 1;
   }
  for (int i = 0; i < size; i++) {
    (*arr)[i] = i * 10;
  }
}

int main() {
  int *numbers = NULL;
  allocateArray(&numbers, 5);

  for (int i = 0; i < 5; i++) {
    printf("%d ", numbers[i]);
  }
  printf("\n");

  if (numbers != NULL)
    free(numbers);
  return 0;
}
```

❗ 실수 방지 팁
| 실수 유형                   | 주의 사항                   |
| --------------------------- | ----------------------- |
| 포인터로 주소를 넘기지 않음             | 원래 변수에 반영되지 않음          |
| `*`와 `&` 혼동                 | `*p`는 값 접근, `&x`는 주소 전달 |
| 이중 포인터 안 쓰면 malloc 결과 반영 안됨 | 꼭 `int **p`로 받기         |


### 1.5.3 Call by Reference: C++에서만 사용 가능
* C++에서는 참조 연산자 &를 사용하여 진짜 참조 전달 가능
* 포인터 없이도 변수의 원본을 직접 수정 가능
```Cpp
#include <iostream>
using namespace std;

void update(int& ref) {
  ref = 100;
}

int main() {
  int num = 10;
  update(num);
  cout << "num: " << num << endl; // 출력: 100
  return 0;
}
```
* int &ref는 num의 별칭(alias) 역할을 하며 직접 수정 가능
  
✅ 연습 문제

1. int x = 5, y = 10;을 교환하는 swap 함수를 Call by Reference(Pointer 이용)로 작성하세요.

2. int *p를 함수에 넘겨 20을 저장하도록 하세요.

3. 함수에서 char *를 동적 할당 받아 "Hello"를 저장하고 출력하세요.

📚 요약
> * C는 기본적으로 Call by Value.
> * 포인터를 사용하면 Call by Reference처럼 동작 가능.
> * C 언어는 Call by Reference를 직접 지원하지 않음
> * 변수 값을 함수 내에서 변경하려면 주소를 전달해야 함.

* C++에서는 & 참조 연산자를 통해 진짜 Call by Reference 구현 가능

* 메모리 할당 및 구조체 처리에도 필수적인 개념.
