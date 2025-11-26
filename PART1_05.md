## 1.5 포인터와 함수 매개변수 전달 
C 언어에서는 함수에 인자를 전달할 때 기본적으로 Call by Value (값에 의한 전달) 방식이 사용됩니다.  
하지만 포인터를 이용하면 Call by Reference (참조에 의한 전달) 효과를 얻을 수 있습니다.

* 함수 인자 전달 방식 요약

| 방식                          | 설명                      | 원본 변수 변경 가능 |
| --------------------------- | ----------------------- | ----------- |
| Call by Value               | 값 복사만 전달                | 불가능       |
| Call by Reference (Pointer) | 주소를 전달하여 원본 접근          | 가능        |
| Call by Reference (C++)     | 참조 연산자 `&` 사용 (포인터 불필요) | 가능 (C++만) |

> C 언어는 **Call by Reference를 지원하지 않지만**, "주소(포인터)를 복사해서 전달"하여 **참조처럼 동작**하게 만듭니다.  

```c
// 함수 호출 방식 비교 (요약)
int x = 10;

f(x);      // Call by Value → x 복사됨
f(&x);     // Call by Pointer → 주소 전달 (C에서 참조 효과)
f(x);      // Call by Reference → C++만 가능 (실제 참조 전달)
```
```txt
Call by Value:
+--------+         +--------+
|   x    | ---->   |   10   |
+--------+         +--------+

함수에서 복사됨

Call by Pointer:
+--------+          +--------+          +--------+
|   x    |  -->     |   p    |  -->     |   10   |
+--------+          +--------+          +--------+
주소 전달 → 원본 직접 수정 가능
```

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

예제 2: Call by Reference (포인터 이용)
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
* Call by Value 실패 예제
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
* Call by Reference(Pointer 이용) 성공 예제
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
예제 4: 이중 포인터: 함수에서 메모리 할당하기  
> 함수 내부에서 메모리를 할당하고 그 결과를 외부 변수에 저장하려면 이중 포인터가 필요합니다.
```c
#include <stdio.h>
#include <stdlib.h>

int allocateArray(int **arr, int size) {
  *arr = malloc(size * sizeof(int)); // C++ type code, (int *)malloc(size * sizeof(int));
  if (*arr == NULL) {
    printf("메모리 할당 실패\n");
    return 1;
   }
  for (int i = 0; i < size; i++) {
    (*arr)[i] = i * 10;
  }
  return 0;
}

int main() {
  int *numbers = NULL;
  if (allocateArray(&numbers, 5) > 0)
    return 1;

  for (int i = 0; i < 5; i++) {
    printf("%d ", numbers[i]);
  }
  printf("\n");

  if (numbers != NULL)
    free(numbers);
  return 0;
}
```
> int **pp가 필요한 이유: 함수가 arr 자체(포인터 변수)를 수정해야 하므로 포인터의 포인터를 받아야 함  

* 실수 방지 체크리스트
| 실수 유형                | 해결 방법 / 주의 사항                      |
| -------------------- | ---------------------------------- |
| 포인터 안 넘기고 값만 전달      | 주소 `&` 를 함수에 전달 (`f(&x)`)          |
| `*`와 `&` 혼동          | `*p`: 값 접근 / `&x`: 주소 전달           |
| malloc 할당 결과가 반영 안 됨 | 포인터 자체를 바꾸려면 이중 포인터 사용 (`int **p`) |
| 메모리 누수 / 해제 누락       | `malloc` 했으면 반드시 `free` 필요         |

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
```c
void swap(int *a, int *b) {
  int temp = *a;
  *a = *b;
  *b = temp;
}
// call
int x = 5, y = 10;
swap(&x, &y);
```
2. int *p를 함수에 넘겨 20을 저장하도록 하세요.
```c
void setToTwenty(int *p) {
  *p = 20;
}

// call
int x;
setToTwenty(&x);
// x == 20
```
3. 함수에서 char *를 동적 할당 받아 "Hello"를 저장하고 출력하세요.
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void createHello(char **str) {
  *str = (char *)malloc(6);  // "Hello" + '\0'
  if (*str == NULL) return;
  strcpy(*str, "Hello");
}

int main() {
  char *message = NULL;
  createHello(&message);

  if (message != NULL) {
    printf("%s\n", message);
    free(message);
  }
  return 0;
}
```

* 요약

| 개념              | 설명                   | 예시                 |
| --------------- | -------------------- | ------------------ |
| Call by Value   | 값 복사, 원본 변경 불가       | `f(x)`             |
| Call by Pointer | 주소 전달, 원본 변경 가능      | `f(&x)`            |
| C에서의 Reference  | 직접 지원 X, 포인터로 우회     | `void f(int *p)`   |
| 이중 포인터          | 포인터를 가리킴, 동적 할당 등 사용 | `void f(int **p)`  |
| C++ 참조          | 참조 전달 문법 존재          | `void f(int &ref)` |

* 메모리 할당 및 구조체 처리에도 필수적인 개념.
