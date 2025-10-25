## 1.0 포인터 기본 개념

 포인터는 C 언어의 가장 핵심적인 개념 중 하나입니다.  
 이 장에서는 포인터를 본격적으로 다루기 전에 반드시 이해해야 할 기초 개념과 구조를 학습합니다.

### 1.0.1 포인터란 무엇인가

* 포인터(pointer)란 `메모리 주소를 저장하는 변수`입니다.
* 일반 변수는 데이터를 저장하고, 포인터는 **그 데이터의 위치(주소)** 를 저장합니다.

```c
int a = 10;       // 일반 변수 선언
int *p = &a;      // 변수 a의 주소를 포인터 p에 저장
                  // int *p; p = &a; 를 줄여 쓴 문장
printf("%d, %p\n", *p, (void*)p);
```
> `&a`: 변수 a의 주소  
> `*p`: p가 가리키는 주소의 값 (즉, a의 값)

| 변수 | 주소 | 값                  |
| -- | ------ | ------------------- |
| a  | 0x1000 | 10                  |
| p  | 0x2000 | 0x1000 (a의 주소)   |
| &a |        | 0x1000              |
| *p |        | 10                  |

📌 *p는 a의 값을 읽는 연산 (간접 참조, dereference)
   int *p; -> "p는 int형 데이터를 가리키는 포인터"

* 중요 개념:
> `&` → 주소 연산자  
> `*` → 간접 참조(역참조) 연산자  
> 포인터를 통해 변수의 값을 읽고 수정 가능  

예제 1: 포인터를 이용한 값 출력
```c
#include <stdio.h>

int main() {
  int x = 42;
  int *ptr = &x;
  printf("x의 값: %d, %p\n", *ptr, (void*)ptr); // 42

  return 0;
}
```
###  1.0.2 메모리와 변수, 주소 개념
* 메모리는 바이트 단위의 연속된 공간으로, 변수는 이 공간의 일부를 사용합니다.
* 변수는 항상 고유한 **주소(address)** 를 가집니다.

주소 얻기
```c
int x = 5;
printf("x의 주소: %p\n", (void*)&x);
```
📌 %p: 포인터(주소)를 출력하는 형식 지정자입니다.

예제 2: 변수의 주소를 출력
```c
#include <stdio.h>

int main() {
  int a = 7;
  float b = 3.14;

  printf("a: %p, b: %p\n", (void*)&a, (void*)&b);

  return 0;
}
```

### 1.0.3 변수와 포인터 관계
* 포인터는 주소를 저장하고, * 연산자를 통해 **간접 참조(역참조)** 로 값을 읽고 수정합니다.

예제 3: 포인터를 이용한 값 변경
```c
#include <stdio.h>

int main() {
  int a = 10;
  int *p = &a;

  printf("Before: a = %d\n", a);

  *p = 20;  // p가 가리키는 메모리의 값을 변경

  printf("After: a = %d\n", a);  // 20

  return 0;
}
```
### 1.0.4 포인터가 필요한 이유
1. 함수 간 데이터 공유 (Call by Reference, 포인터 이용)
```c
void modify(int *p) {
  *p = 100;
}

int main() {
  int a = 5;
  
  modify(&a);
  printf("%d\n", a);  // 100

  return 0;  
}
```
2. 동적 메모리 할당  
  * 실행 중 필요한 만큼 메모리 할당 (e.g., malloc, new)

3. 배열 및 문자열 처리
   * 포인터를 이용해 인덱스 연산 및 순회 기능
```c
char *str = "Hello";
printf("%c\n", *(str + 1));  // 'e'
printf("%c\n", str[1]);      // 'e'
```
```c
char *str = "Hello";     // 문자열 상수 (읽기 전용)
char arr[] = "Hello";    // 배열 (수정 가능)
```
  * 문자열 상수( `char *`)는 읽기 전용이므로 수정 불가
```c
char *str = "Hello";
str[0] = 'h'; // 오류발생(읽기 전용 메모리)
```

4. 동적 자료구조 구현  
  * 연결 리스트, 트리, 그래프 등 메모리 연결 구조 표현에 필수

### 1.0.5 포인터 크기와 자료형
* 포인터의 크기는 시스템 아키텍처에 따라 다릅니다.

| 시스템  | 포인터 크기 |
| ------- | ---------- |
| 32비트  | 4바이트   |
| 64비트  | 8바이트   |

포인터 자료형은 해당 주소를 어떤 방식으로 해석할지를 결정합니다.  
따라서 형 변환 없이 잘못된 자료형으로 접근할 경우, 예기치 않은 결과가 발생할 수 있습니다.

예제 4: 포인터 크기 확인
```c
#include <stdio.h>

int main() {
  int *ip;
  double *dp;
  char *cp;
  
  printf("int * 크기: %zu\n", sizeof(ip));
  printf("double * 크기: %zu\n", sizeof(dp));
  printf("char * 크기: %zu\n", sizeof(cp));
  
  return 0;
}
```
📌 sizeof(ip)는 포인터 자체의 크기, 즉 주소값의 크기를 나타냅니다.
* `sizeof(ip)`와 `sizeof(*ip)`의 차이
 ```c
 int *ip;
 printf("%zu\n", sizeof(ip));   // 포인터 크기 (ex: 8)
 printf("%zu\n", sizeof(*ip));  // int 크기 (ex: 4)
 ```
| 표현식     |  의미                      |
| ---------- | -------------------------- |
| sizeof(ip) | 포인터 자체 크기            |
| sizeof(*ip)| 포인터가 가리키는 타입의 크기 | 

예제 5: 포인터 타입에 따른 차이
```c
int a = 100;
int *ip = &a;
char *cp = (char*)&a;

printf("%d\n", *ip); // 100
printf("%d\n", (unsigned char)*cp);  // 하위 1바이트만 읽음
```
📌 주소는 같아도, 자료형에 따라 읽는 바이트 수가 달라집니다.

### 1.0.6 포인터와 메모리 레이아웃 이해
C 프로그램의 메모리 구조는 일반적으로 다음과 같습니다:

```text

  높은 주소
  +-------------+
  | 스택(Stack) | ← 지역 변수, 함수 호출, main() 함수
  +-------------+
  |  힙(Heap)   | ← 동적 메모리 (malloc, new 등)
  +-------------+
  | 데이터 영역  | ← 전역, 정적 변수
  +-------------+
  |  코드 영역   | ← 프로그램 실행 코드
  +-------------+
  낮은 주소
```
📌스택이 높은 주소에 위치하는 구조가 일반적이지만, 반드시 그런 것은 아닙니다.

예제 6: 힙과 스택, 데이터의 주소 비교
```c
#include <stdio.h>
#include <stdlib.h>

int global_var = 100; // 데이터 영역

int main() {
  int local_var = 10;        // 스택
  int *heap_var = malloc(sizeof(int)); // 힙
  if (heap_var != NULL) return 1;
  *heap_var = 20;

  printf("data, global_var: %p\n", (void*)&global_var);
  printf("stack, local_var : %p\n", (void*)&local_var);
  printf("heap, heap_var  : %p\n", (void*)heap_var);

  free(heap_var);

  return 0;
}
```
📌 대부분의 시스템에서 스택은 가장 높은 주소 영역에, 그 아래로 힙, 데이터, 코드 순으로 배치됩니다. 
   (하지만 이는 시스템 구조에 따라 달라질 수 있습니다.)
* 힙과 스택의 성장 방향(스택 ↓ / 힙 ↑)

✅ 연습 문제
1. 정수형 변수 a를 선언하고 포인터를 이용하여 값을 변경하시오.  
  (힌트: 포인터를 선언하고 *p = 값 형식으로 접근)
```c
int a = 10;
int *p = &a;
*p = *p + 5;
printf("%d", a);  // 15
```
2. 두 개의 정수를 더하는 함수를 만들되, 포인터를 사용하여 결과를 호출자에게 전달하시오.
```c
#include <stdio.h>

void add(int a, int b, int *result) {
    *result = a + b;
}

int main() {
    int x = 3, y = 4, sum = 0;
    add(x, y, &sum);
    printf("합계: %d\n", sum);
    return 0;
}
```
3. 포인터를 이용해 문자열을 출력하는 코드를 작성하시오.  
  (힌트: char *str = "Hello"; → while (*str) 루프 사용 가능)
 ```c
 #include <stdio.h>
 
 int main() {
     char *str = "Hello";
     while (*str) { // or while (*str != '\0'), 포인터는 배열처럼 순회 가능하다.
         printf("%c ", *str); // or putchar(*str)
         str++;
     }
     return 0;
 }
 ```
📚 요약
| 항목     | 설명                     | 예시                    |
| ------ | ---------------------- | --------------------- |
| 포인터    | 메모리 주소를 저장하는 변수                    | `int *ptr = &a;`     |
| `&`연산자 | 주소 연산자 (`&a` → 변수 a의 주소)            | `&a`                  |
| `*`연산자 | 간접 참조(역참조) 연산자 (`*ptr` → 주소의 값)  | `*ptr = 20;`          |
| 포인터 크기 | 시스템(32/64bit)에 따라 다름                | `sizeof(int *)`       |
| 메모리 구조 | 코드, 데이터, 힙, 스택                      | 메모리 레이아웃 다이어그램 참조  |
| 자료형 중요성| 포인터 해석은 자료형에 의존함               | 'char *' vs 'int *'   |

