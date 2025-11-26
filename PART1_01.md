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
  char c = 'A';
  char d = 'B';
  
  printf("%c, %p, %c\n", c, (void *)&c, *&c); 
  printf("%c, %p, %c\n", d, (void *)&d, *&d); 

  return 0;
}
```
✔ 실행 결과 (예시):
```text
A, 000000000062FE1F, A
B, 000000000062FE1E, B
```

```c
#include <stdio.h>

int main() {
  int a = 10;

  printf("a의 값, 주소, 값: %d, %p, %d\n", a, (void *)&a, *&a);
  // %p 변환 지정자는 반드시 (void *) 타입의 포인터를 인자로 받아야 합니다.
  // 다른 타입의 포인터를 직접 전달하면 C 표준에서는 Undefined Behavior(UB)가 발생합니다.

  printf("&a+1의 주소: %p\n", (int *)&a+1);
  printf("&a+2의 주소: %p\n", (int *)&a+2);
  
  return 0;
}
```
✔ 실행 결과 (예시):
```text
a의 값, 주소, 값: 10, 000000000062FE1C, 10
&a+1의 주소: 000000000062FE20
&a+2의 주소: 000000000062FE24
```
> (int *)&a + 1은 int 타입 크기(4바이트) 만큼 주소가 증가합니다  
> Tip: 주소 차이를 바이트 단위로 보고 싶으면 (char *)로 형 변환 필요  

예제 1: 두 변수의 주소 출력
```c
#include <stdio.h>

int main() {
  int x = 5, y = 20;

  printf("x의 주소: %p\n", (void *)&x);
  printf("y의 주소: %p\n", (void *)&y);
  printf("주소 차이 (char 단위): %td\n", (char *)&y - (char *)&x); → 바이트 단위
  printf("주소 차이 (int 단위) : %td\n", (int *)&y - (int *)&x);   → int 단위

  return 0;
}
```
✔ 실행 결과 (예시):
```text
x의 주소: 000000000062FE1C
y의 주소: 000000000062FE18
주소 차이 (char 단위): -4
주소 차이 (int 단위) : -1
```  
> 이 예제를 통해 서로 다른 변수는 서로 다른 주소를 갖는다는 것을 확인할 수 있습니다.  
> 주소 차이를 구할 때는 바이트 단위로 보고 싶으면 `(char *)`로 변환해야 합니다.  

### 1.1.2. 간접 참조(역참조) 연산자 `*`
* 포인터를 통해 메모리 주소에 저장된 실제 값에 접근할 수 있습니다.
* 이를 `간접 참조(indirect reference)` 또는 `역참조(dereference)`라고 합니다.
```c
#include <stdio.h>

int main() {
  int a = 42;
  int *ptr = &a;  // a의 주소를 ptr에 저장
  
  printf("a의 주소: %p\n", (void *)&a);
  printf("ptr가 가리키는 주소: %p\n", ptr);
  printf("ptr가 가리키는 값: %d, %d\n", *ptr, *&a);  // a의 값 출력
  
  return 0;
}
```
✔ 실행 결과 (예시):
```text
a의 주소: 000000000062FE14
ptr가 가리키는 주소: 000000000062FE14
ptr가 가리키는 값: 42, 42
```
> `*ptr`은 ptr이 **가리키는 메모리 주소에 저장된 실제 값**을 의미합니다.  

예제 2: 포인터를 이용한 값 변경
```c
#include <stdio.h>

int main() {
    int num = 100;
    int *ptr = &num;  // num의 주소를 ptr에 저장

    printf("num 초기 값: %d\n", num);

    *ptr = 200;  // ptr가 가리키는 주소(num)에 200을 저장, 즉 num = 200; 동일한 효과

    printf("num 변경된 값: %d, %d, %d\n", num, *ptr, *&num);

    return 0;
}
```
> 결과: num의 값이 100 → 200으로 변경됩니다.  
> *ptr = 값; → ptr이 가리키는 주소에 값 저장

예제 3: 값 전달(Call by Value) vs 포인터 전달(Call by Reference)
```c
#include <stdio.h>

void change_by_value(int x) {
    x = 999;
}

void change_by_pointer(int *ptr) {
  if(ptr == NULL) {
    printf("잘못된 포인터입니다.\n");
    return;    
  }
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
> change_by_value() 값 전달은 원본을 복사하므로 변경 불가  
> change_by_pointer() 포인터 전달은 원본의 주소를 넘기므로 값 수정 가능  

* 요약  

| 연산자 | 이름         | 설명                      | 예시                                     |
| --- | ---------- | ----------------------- | -------------------------------------- |
| `&` | 주소 연산자     | 변수의 메모리 주소를 얻음          | `&a` → 변수 `a`의 주소                      |
| `*` | 간접 참조(역참조) | 포인터가 가리키는 주소의 값을 읽거나 변경 | `*ptr = 20;` → `ptr`가 가리키는 위치에 `20` 저장 |

* 실전 Tip
 
* *ptr = 값; 형태는 ptr가 가리키는 주소에 값을 대입하는 의미입니다.
* 초기화되지 않은 포인터는 예측할 수 없는 메모리를 참조할 수 있으며, 에러나 충돌을 유발할 수 있습니다.
* 안전하게 사용하려면 NULL 초기화
```c
int *ptr = NULL;
if(ptr != NULL) printf("%d", *ptr);
```
  
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
> NULL은 <stddef.h> 또는 <stdio.h> 포함 시 정의되어 있지만, C99 이후엔 #include <stddef.h>가 정식임  
> C++에서는 NULL 대신 nullptr 사용 권장. C11에서도 nullptr은 존재하지 않음  

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
  if(a == NULL || b == NULL) {
    printf("잘못된 포인터입니다.\n");
    return;    
  }
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

printf("a: %d, %d, 주소: %p, 주소 크기: %zu\n", *pa, *&a, pa, sizeof(pa));
printf("b: %d, %d, 주소: %p, 주소 크기: %zu\n", *pb, *&b, pb, sizeof(pb));
printf("c: %d, %d, 주소: %p, 주소 크기: %zu\n", *pc, *&c, pc, sizeof(pc));
```
