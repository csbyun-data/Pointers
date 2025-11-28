## 1.11 구조체와 포인터
* 구조체와 포인터는 실전 프로그램에서 자주 함께 사용됩니다.
* 구조체는 여러 데이터를 묶어 표현하는 데 유용하며, 포인터와 결합하면 메모리 동적 할당, 함수 전달, 연결 리스트와 같은 고급 자료구조 구현이 가능합니다.

### 1.11.1 구조체 선언과 멤버 접근
* 구조체 정의 및 사용
```c
#include <stdio.h>

struct Point { int x; int y; };

int main() {
  struct Point p1 = {10, 20};
  printf("x: %d, y: %d\n", p1.x, p1.y);
  return 0;
}
```

> . 연산자로 멤버에 접근합니다.  

* struct 키워드 생략하려면 typedef 사용:
```c
typedef struct { int x; int y; } Point;
Point p2 = {30, 40};
```
* 구조체의 포인터 멤버변수
```c
#include <stdio.h>
typedef struct {
  char *pcarr;
  int *piarr;
} Data;

int main() {
  char carr[3] = {'A', 'B', 'C'};
  int iarr[3] = {10, 20, 30};

  Data s1 = { carr, iarr};

  printf("%c %c %c\n", s1.pcarr[0], s1.pcarr[1], s1.pcarr[2]);
  printf("%d %d %d\n", s1.piarr[0], s1.piarr[1], s1.piarr[2]);
}
```

### 1.11.2 구조체 포인터 선언과 사용
* 구조체 포인터 사용
```c
#include <stdio.h>

typedef struct { int x; int y; } Point;

int main() {
  Point p = {10, 20};
  Point *ptr = &p;
  Point **pptr = &ptr;

  printf(" %d, %d\n", p.x, p.y);  
  printf(" %d, %d, %d, %d\n", ptr->x, ptr->y, (*ptr).x, (*ptr).y);
  printf(" %d, %d, %d, %d\n", (*pptr)->x, (*pptr)->y, (**pptr).x, (**pptr).y);

  return 0;
}
```
> ptr->x는 (*ptr).x와 동일합니다.

### 1.11.3 구조체 배열과 포인터
* 구조체 1차원 배열, 요소 접근
```c
#include <stdio.h>

typedef struct {
  char name[20];
  int age;
} Person;

int main() {
  Person arr[2] = {{"Alice", 25}, {"Bob", 30}};
  Person *p = arr;

  for (int i = 0; i < 2; i++) {
    printf("%s %d, ", arr[i].name, arr[i].age);
    printf("%s %d, ", (arr+i)->name, (arr+i)->age);
    printf("%s %d\n", (*(arr+i)).name, (*(arr+i)).age);
  }

  return 0;
}
```
* 구조체 1차원 배열 과 포인터 산술 연산 접근
```c
#include <stdio.h>

typedef struct {
  char name[20];
  int age;
} Person;

int main() {
  Person arr[2] = {{"Alice", 25}, {"Bob", 30}};
  Person *p = arr;

  for (int i = 0; i < 2; i++) {
    printf("%s %d, ", p[i].name, p[i].age);
    printf("%s %d, ", (p+i)->name, (p+i)->age);
    printf("%s %d\n", (*(p+i)).name, (*(p+i)).age);
  }

  return 0;
}
```
> 구조체 배열은 포인터 산술 연산과 함께 자주 사용됩니다.

* 구조체 2차원 배열과 포인터 산술 연산 접근
```c
#include <stdio.h>

typedef struct {
  int x;
  int y;
} Point;

int main() {
  Point arr[2][2] = {{{0, 0}, {1, 1}}, {{2, 2}, {3, 3}}};
  Point (*ptr)[2] = arr;

  printf("%d %d %d, ", **arr, *arr[0], *arr[1]);
  printf("%p %p %p\n", (void *)arr, (void *)arr[0], (void *)arr[1]);
  
  printf("%d %d %d, ", **(arr+1), *(arr[0]+1), *(arr[1]+1));
  printf("%p %p %p\n\n", (void *)(arr+1), (void *)(arr[0]+1), (void *)(arr[1]+1));

  printf("%d %d %d, ", **ptr, *ptr[0], *ptr[1]);
  printf("%p %p %p\n", (void *)ptr, (void *)ptr[0], (void *)ptr[1]);
  
  printf("%d %d %d, ", **(ptr+1), *(ptr[0]+1), *(ptr[1]+1));
  printf("%p %p %p\n", (void *)(ptr+1), (void *)(ptr[0]+1), (void *)(ptr[1]+1));

  return 0;
}  
```

### 1.11.4 동적 구조체 배열 생성 및 해제
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct {
  char name[20];
  int age;
} Person;

int main() {
  int count = 3;
  Person *list = malloc(count * sizeof(Person)); //C++ (Person *)malloc(count * sizeof(Person));

  if (!list) {
    printf("메모리 할당 실패\n");
    return 1;
  }

  for (int i = 0; i < count; i++) {
    printf("이름 입력: ");
    scanf("%s", list[i].name);
    printf("나이 입력: ");
    scanf("%d", &list[i].age);
  }

  printf("\n[입력 결과]\n");
  for (int i = 0; i < count; i++) {
    printf("%s (%d세)\n", list[i].name, list[i].age);
  }

  free(list);
  return 0;
}
```

### 1.11.5 중첩 구조체와 포인터 멤버
* 중첩 구조체와 포인터 접근
```c
#include <stdio.h>

typedef struct {
  int year;
  int month;
  int day;
} Date;

typedef struct {
  char name[20];
  Date birthday;
} Student;

int main() {
  Student s = {"Jane", {2000, 5, 14}};
  Student *ps = &s;

  printf("%s의 생일은 %d년 %d월 %d일입니다.\n",
         ps->name, ps->birthday.year, ps->birthday.month, ps->birthday.day);

  return 0;
}
```

예제 1: 구조체를 함수 인자로 전달 (포인터 방식)
```c
#include <stdio.h>

typedef struct {
  int x;
  int y;
} Point;

void move(Point *p, int dx, int dy) {
  p->x += dx;
  p->y += dy;
}

int main() {
  Point pt = {0, 0};
  move(&pt, 5, 10);
  printf("이동 후 위치: (%d, %d)\n", pt.x, pt.y);
  return 0;
}
```
### 1.11.6 구조체를 반환과  포인터 구조체를 반환하는 방식
* 구조체를 값으로 반환
> 장점  
> 메모리 관리가 간단 (함수 내부에서 자동 할당 → 반환 시 복사)  
> 안전성 높음 (포인터처럼 dangling pointer 위험 없음)

> 단점  
> 구조체 크기가 클 경우 복사 비용 발생  
> 스택 메모리 사용 → 너무 큰 구조체는 부담  

```c
typedef struct {
  int x;
  int y;
} Point;

Point createPoint(int a, int b) {
  Point p;
  p.x = a;
  p.y = b;
  return p;  // 값 복사
}

int main() {
  Point pt = createPoint(10, 20);
  printf("(%d, %d)\n", pt.x, pt.y);
  return 0;
}
```
* 구조체 포인터를 반환
> 장점  
> 복사 비용 없음 (주소만 전달)  
> 동적 할당으로 구조체 크기 제한 없음

> 단점  
> malloc 사용 → 메모리 해제 책임은 호출자에게 있음
> 잘못 관리하면 메모리 누수 또는 dangling pointer 발생

```c
#include <stdlib.h>
typedef struct
{
  int x;
  int y;
} Point;

Point* createPoint(int a, int b)
{
  Point* p = malloc(sizeof(Point));
  if (!p) return NULL;
  p->x = a;
  p->y = b;
  return p;  // 포인터 반환
}

int main()
{
  Point* pt = createPoint(10, 20);
  printf("(%d, %d)\n", pt->x, pt->y);
  free(pt);  // 반드시 해제
  return 0;
}
```
📚 요약
| 내용             | 설명                                   | 연산자, 설       |
| -------------- | ------------------------------------ | ---------- |
| 구조체 선언 | struct Point { int x; int y; }; |  |
| typedef 사용 | typedef struct { int x; int y; } Point; | struct 키워드 생략 가능|
| 구조체 멤버 접근      | `structVar.member`                   | `.`        |
| 구조체 포인터 접근  | `ptr->member` 또는 `(*ptr).member`   | `->`       |
| 구조체 배열 접근      | `arr[i].member` 또는 `(p + i)->member` | 포인터 산술 연산 가능  |
| 함수 전달 시 포인터 추천 | func(&p)             | 값 복사보다 포인터로 전달 |
| 동적 할당 | malloc(sizeof(Point)) | 힙 메모리 사용, free()로 해제 필요|
| 중첩 구조체 | ps->birthday.year | |
| 반환 방식 | 값 반환: return p;  포인터 반환: return malloc(...) | 값 반환은 안전, 포인터 반환은 효율적|


