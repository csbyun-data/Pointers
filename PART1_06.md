📘 1.6 포인터 유형

C 언어에서 포인터는 정확한 메모리 주소를 가리키는 것이 중요합니다. 그러나 실제 프로그램에서는 다양한 유형의 포인터가 사용되며, 잘못된 포인터 사용은 프로그램 오류나 버그의 원인이 됩니다.

🔍 1.6.1 NULL 포인터  
✅ 설명

* 아무것도 가리키지 않는 포인터.

* 일반적으로 포인터를 선언 후 초기화하지 않으면 쓰레기 값이 들어가므로 NULL로 초기화하는 습관이 중요.
* `NULL`은 `<stddef.h>` 또는 `<stdio.h>`에 정의됨

🧪 예제
```c
#include <stdio.h>

int main() {
    int *ptr = NULL;

    if (ptr == NULL) {
        printf("ptr는 현재 NULL입니다.\n");
    }

    return 0;
}
```
⚠️ 주의

* NULL 포인터를 역참조하면 segmentation fault 발생.

```c
// 잘못된 예
int *p = NULL;
*p = 100;  // 런타임 오류 발생
```

🔍 1.6.2 Void 포인터, Generic Pointer (void *)  
✅ 설명

* 타입이 정해지지 않은 포인터.

* 다양한 자료형의 주소를 저장할 수 있는 범용 포인터.

* 역참조(*)하려면 반드시 형변환해야 함.

🧪 예제: void 포인터 사용
```c
#include <stdio.h>

int main() {
    int a = 10;
    void *vp;

    vp = &a;
    printf("정수 값: %d\n", *(int *)vp); // 반드시 형변환

    return 0;
}
```
🔁 활용 예: 여러 타입 처리
```c
void printValue(void *data, char type) {
    switch (type) {
        case 'i': printf("정수: %d\n", *(int *)data); break;
        case 'f': printf("실수: %.2f\n", *(float *)data); break;
        case 'c': printf("문자: %c\n", *(char *)data); break;
    }
}

int main() {
    int i = 42;
    float f = 3.14;
    char c = 'A';

    printValue(&i, 'i');
    printValue(&f, 'f');
    printValue(&c, 'c');
    return 0;
}
```
⚠️ 주의

* void *는 산술 연산이 불가능 (vp++ 불가).

* 반드시 형변환 후 사용.

🔍 1.6.3 Wild 포인터와 Dangling 포인터

✅ Wild 포인터 설명

* 초기화되지 않은 포인터.

* 쓰레기 값을 가지므로 임의의 메모리를 참조하게 되어 예측할 수 없는 결과 초래.

🧪 잘못된 예제 (사용 금지)
```c
int *ptr;   // 초기화되지 않음 (wild pointer)
*ptr = 100; // 런타임 오류, 위험!
```
✅ 해결

* 포인터 선언 후 초기화는 필수!
```c
int *ptr = NULL;
```

✅ Dangling 포인터 설명

* 유효하지 않은 메모리를 참조하는 포인터.

* 보통 메모리 해제 후, 여전히 포인터가 해당 주소를 가리킬 때 발생.

🧪 예제: 해제 후 사용 금지
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *ptr = (int *)malloc(sizeof(int));
    *ptr = 100;
    free(ptr);  // 메모리 해제

    // *ptr = 200; // 위험! 이미 해제된 메모리 접근 (dangling)
    
    ptr = NULL; // 이렇게 해제 후 NULL로 초기화해야 안전
    return 0;
}
```
```C
int* wild; // 초기화되지 않음 → 위험
int* dangling;
dangling = (int*)malloc(sizeof(int));
free(dangling);
// 이후 dangling 사용 → 위험한 접근
```
🔍 1.6.4 (const * ) 와 ( * const)  
✅ 설명  
C 언어에서 `const` 키워드는 **값의 변경을 제한**하는 데 사용됩니다.   
포인터와 함께 사용할 때는 **가리키는 값이 변경 불가능한지**, 또는 **포인터 자체가 변경 불가능한지**에 따라 의미가 달라집니다.   

🧭 const int* ptr (포인터 to const)
* `ptr`은 **const int를 가리키는 포인터**
* 즉, `*ptr`의 값을 **변경할 수 없음** 하지만 `ptr` 자체는 다른 주소로 **변경 가능**
```C
int x = 10;
int y = 20;
const int* ptr = &x;

*ptr = 30;     // ❌ 오류: 값 변경 불가
ptr = &y;      // ✅ 가능: 다른 주소로 변경 가능
```
🧭 int* const ptr (const 포인터)
* ptr은 int를 가리키는 상수 포인터
* 즉, ptr 자체는 다른 주소로 변경 불가 하지만 *ptr의 값은 변경 가능
```C
int x = 10;
int y = 20;
int* const ptr = &x;

*ptr = 30;     // ✅ 가능: 값 변경 가능
ptr = &y;      // ❌ 오류: 주소 변경 불가
```
🧭 const int* const ptr (상수 포인터 to 상수 값)
* ptr은 const int를 가리키는 상수 포인터

* 즉, *ptr의 값도 변경 불가, ptr 자체도 변경 불가
```C
int x = 10;
const int* const ptr = &x;

*ptr = 20;     // ❌ 오류: 값 변경 불가
ptr = &y;      // ❌ 오류: 주소 변경 불가
```
📊 const 포인터 유형 요약 비교표

| 선언 방식               | 값 변경 가능 | 주소 변경 가능 | 설명                             |
|------------------------|--------------|----------------|----------------------------------|
| `const int* ptr`       | ❌           | ✅             | 값은 변경 불가, 주소는 변경 가능 |
| `int* const ptr`       | ✅           | ❌             | 값은 변경 가능, 주소는 변경 불가 |
| `const int* const ptr` | ❌           | ❌             | 값도 주소도 변경 불가            |

📌 요약 정리
| 포인터 유형       | 의미                | 주의 사항          |
| ------------ | ----------------- | -------------- |
| `NULL` 포인터   | 아무것도 가리키지 않음      | 역참조하면 오류 발생    |
| `void *` 포인터 | 형이 없는 범용 포인터      | 반드시 형변환 후 사용   |
| Wild 포인터     | 초기화되지 않은 포인터      | 쓰기/읽기 모두 위험    |
| Dangling 포인터 | 해제된 메모리를 가리키는 포인터 | 해제 후 `NULL` 처리 |

✅ 연습 문제

1. void * 포인터를 이용해 int, float, char 값을 출력하는 함수를 만들어보세요.

2. 동적 할당한 메모리를 해제한 후 포인터를 NULL로 설정하는 코드 예제를 작성하세요.

3. 다음 코드의 오류를 설명하세요.
```c
int *p;
*p = 10;
```
