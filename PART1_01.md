## 1.1 변수 주소 구하기(&), 간접 참조(*)     
 앞 장에서 포인터의 개념을 배웠다면, 이번에는 실제로 주소를 구하고 값을 참조하는 방법을 코드로 직접 확인해봅시다.
* **포인터(pointer)** 는 "어떤 변수의 메모리 주소를 저장하는 변수"입니다.
* 포인터를 이해하기 위해서는 다음 두 가지 연산자를 명확히 이해해야 합니다.

| 연산자 | 이름         | 기능 설명                             |
| --- | --------------- | ------------------------------------- |
| `&` | 주소 연산자      | 변수의 **메모리 주소**를 얻음          |
| `*` | 간접 참조(역참조) | 포인터가 가리키는 **주소의 값을 읽거나 수정** |

### 1.1.1 주소 연산자 &  
* 변수 앞에 &를 붙이면, 해당 변수의 **메모리 주소** 를 구할 수 있습니다.
```c
#include <stdio.h>

int main() {
  int a = 10;

  printf("a의 값: %d\n", a);
  printf("a의 주소: %p\n", (void*)&a); // %p는 포인터(주소) 출력
  // (표준적으로 %p에는 (void*)를 사용하는 것이 권장 됩니다.)
  return 0;
}
```
✔ 실행 결과 (예시):
```text
a의 값: 10
a의 주소: 0x7ffeeb4c5b5c
```
  * 주소 값은 시스템/컴파일러에 따라 달라질 수 있습니다.

예제 1: 두 변수의 주소 출력
```c
#include <stdio.h>

int main() {
  int x = 5, y = 20;

  printf("x의 주소: %p\n", &x);
  printf("y의 주소: %p\n", &y);
  printf("주소 차이: %td\n", (char *)&y - (char *)&x);

  return 0;
}
```  
이 예제를 통해 서로 다른 변수는 서로 다른 주소를 갖는다는 것을 확인할 수 있습니다.

### 1.1.2. 간접 참조(역참조) 연산자 `*`
* 포인터를 통해 메모리 주소에 저장된 실제 값에 접근할 수 있습니다.
* 이를 `간접 참조(indirect reference)` 또는 `역참조(dereference)`라고 합니다.
```c
#include <stdio.h>

int main() {
  int a = 42;
  int *ptr = &a;  // a의 주소를 ptr에 저장
  
  printf("a의 주소: %p\n", &a);
  printf("ptr가 가리키는 주소: %p\n", ptr);
  printf("ptr가 가리키는 값: %d\n", *ptr);  // a의 값 출력
  
  return 0;
}
```
✔ 실행 결과 (예시):
```text
ptr가 가리키는 주소: 0x7ffde6a7d43c
ptr가 가리키는 값: 42
```
* *ptr은 ptr이 **가리키는 메모리 주소의 값** 을 의미합니다.

예제 2: 포인터를 이용한 값 변경
```c
#include <stdio.h>

int main() {
    int num = 100;
    int *ptr = &num;  // num의 주소를 ptr에 저장

    printf("num 초기 값: %d\n", num);

    *ptr = 200;  // ptr가 가리키는 주소(num)에 200을 저장, 즉 num = 200; 동일한 효과

    printf("num 변경된 값: %d\n", num);

    return 0;
}
```
* 결과: num의 값이 100 → 200으로 변경됩니다.
📌 *ptr = 값; 은 ptr이 가리키는 주소에 값을 대입하는 의미입니다.

예제 3: 값 전달(Call by Value) vs 포인터 전달(Call by Reference)
```c
#include <stdio.h>

void change_by_value(int x) {
    x = 999;
}

void change_by_pointer(int *ptr) {
    *ptr = 888;
}

int main() {
    int data = 100;

    change_by_value(data);
    printf("값 전달 후: %d\n", data);  // 100

    change_by_pointer(&data);
    printf("포인터 전달 후: %d\n", data);  // 888

    return 0;
}
```
✔ 출력 결과:
```text
값 전달 후: 100
포인터 전달 후: 888
```
📌 change_by_value() 값 전달은 원본을 복사하므로 변경 불가
📌 change_by_pointer() 포인터 전달은 원본의 주소를 넘기므로 값 수정 가능

✅ 연습 문제

1. int x = 50;일 때, 포인터 ptr를 이용해 x의 값을 75로 변경하는 프로그램을 작성하세요.
```c
int x = 50;
int *ptr = &x;
*ptr = 75;
printf("%d", x);  // 출력: 75
```
2. 두 정수의 값을 포인터를 사용해 서로 바꾸는 swap() 함수를 작성하세요.
```c
void swap(int *a, int *b) {
  int temp = *a;
  *a = *b;
  *b = temp;
}

int main() {
  int a=3, b=5;
  swap(&a, &b);
  printf("a=%d, b=%d\n", a, b); // a=5, b=3

  return 0;
}
```

3. 세 개의 정수를 선언하고 각각 포인터를 사용해 주소와 값을 출력해보세요.  
   포인터 변수 간의 관계를 관찰하며 포인터가 메모리를 어떻게 추적하는지 이해해보세요.
```c
int a = 1, b = 2, c = 3;
int *pa = &a, *pb = &b, *pc = &c;

printf("a: %d, 주소: %p, 포인터 크기: %zu\n", *pa, pa, sizeof(pa));
printf("b: %d, 주소: %p, 포인터 크기: %zu\n", *pb, pb, sizeof(pb));
printf("c: %d, 주소: %p, 포인터 크기: %zu\n", *pc, pc, sizeof(pc));
```

📚 요약
| 연산자 | 이름         | 설명                      | 예시                                     |
| --- | ---------- | ----------------------- | -------------------------------------- |
| `&` | 주소 연산자     | 변수의 메모리 주소를 얻음          | `&a` → 변수 `a`의 주소                      |
| `*` | 간접 참조(역참조) | 포인터가 가리키는 주소의 값을 읽거나 변경 | `*ptr = 20;` → `ptr`가 가리키는 위치에 `20` 저장 |

💡 실전 Tip
* *ptr = 값; 형태는 ptr가 가리키는 주소에 값을 대입하는 의미입니다.
* 초기화되지 않은 포인터는 예측할 수 없는 메모리를 참조할 수 있으며, 에러나 충돌을 유발할 수 있습니다.
  
```c
#include <stdio.h>

int main() {
  int *ptr = NULL;  // 포인터 초기화: 아직 유효한 주소 없음

  if (ptr != NULL) {
    printf("ptr가 가리키는 값: %d\n", *ptr);  // 안전하게 접근
  } else {
    // NULL 포인터 역참조는 세그먼테이션 오류를 유발할 수 있음    
    printf("ptr는 아직 유효한 주소를 가리키지 않습니다.\n");
  }

  return 0;
}
```
* C++에서는 NULL 대신 nullptr 사용을 권장합니다.
