## 1.11 구조체와 포인터
* 구조체와 포인터는 실전 프로그램에서 자주 함께 사용됩니다.
* 구조체는 여러 데이터를 묶어 표현하는 데 유용하며, 포인터와 결합하면 메모리 동적 할당, 함수 전달, 연결 리스트와 같은 고급 자료구조 구현이 가능합니다.

### 1.11.1 구조체 선언과 멤버 접근
* 구조체 정의 및 사용
```c
#include <stdio.h>

struct Point {
  int x;
  int y;
};

int main() {
  struct Point p1 = {10, 20};
  printf("x: %d, y: %d\n", p1.x, p1.y);
  return 0;
}
```

* . 연산자로 멤버에 접근합니다.
* struct 키워드 생략하려면 typedef 사용:
```c
typedef struct {
  int x;
  int y;
} Point;
```

### 1.11.2 구조체 포인터 선언과 사용
* 구조체 포인터 사용
```c
#include <stdio.h>

typedef struct {
  int x;
  int y;
} Point;

int main() {
  Point p = {10, 20};
  Point *ptr = &p;

  printf("x: %d, y: %d\n", ptr->x, ptr->y);  // -> 연산자 사용
  return 0;
}
```
* ptr->x는 (*ptr).x와 동일합니다.

### 1.11.3 구조체 배열과 포인터
* 구조체 배열 선언과 접근
```c
typedef struct {
  char name[20];
  int age;
} Person;

int main() {
  Person arr[2] = {{"Alice", 25}, {"Bob", 30}};
  Person *p = arr;

  for (int i = 0; i < 2; i++) {
    printf("%s (%d세)\n", (p + i)->name, (p + i)->age);
  }

  return 0;
}
```
* 구조체 배열은 포인터 산술 연산과 함께 자주 사용됩니다.

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
  Person *list = (Person *)malloc(count * sizeof(Person));

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

추가 예제 1: 구조체를 함수 인자로 전달 (포인터 방식)
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
📝 요약
| 내용             | 설명                                   | 연산자        |
| -------------- | ------------------------------------ | ---------- |
| 구조체 멤버 접근      | `structVar.member`                   | `.`        |
| 구조체 포인터 멤버 접근  | `ptr->member` 또는 `(*ptr).member`     | `->`       |
| 구조체 배열 접근      | `arr[i].member` 또는 `(p + i)->member` | 포인터 산술 가능  |
| 함수 전달 시 포인터 추천 | 값 복사보다 포인터로 전달                       | 효율성, 수정 가능 |
