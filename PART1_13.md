## 1.13 함수 포인터

### 1.13.1 함수 포인터 개념

예제 1: 함수 포인터 사용
```c
#include <stdio.h>

void func(void);

int main() {
  void (* funcPointer)(void) = func();  //함수 포인터 변수 선언
  funcPointer();

  return 0;
}

void func(void) {
  printf(" Function Pointer Ex.\n");
}
```

예제 2: 함수 포인터 사용
```
#include <stdio.h>
#pragma warning(disable:4996)

void func1(void);
void func2(void);
void func3(void);

int main(void) {
  void (* func)(void) = NULL;
  int menu;

  while(1) {
    printf("[0] Quit, [1] Func1, [2] Func2, [3]Func3 - ");
    scanf("%d", &menu);

    if(menu < 0 || menu > 3)
      continue;
    if(menu == 0)
      break;
    switch(menu) {
      case 1: func = func1; break;
      case 2: func = func2; break;
      case 3: func = func3; break;
      default :
    }
    func();
  }
}
void func1(void) {
  printf("func1 ex\n");
}
void func2(void) {
  printf("func2 ex\n");
}
void func3(void) {
  printf("func3 ex\n");
}
```

예제 3: 함수 포인터 배열 사용
```
#include <stdio.h>
#pragma warning(disable:4996)

void func1(void);
void func2(void);
void func3(void);

int main(void) {
  void (* funcArray[])(void) = {func1, func2, func3 }; //함수 포인터 배열 선언, []갯수는 컴파일시 3 입력
  int menu;

  while(1) {
    printf("[0] Quit, [1] Func1, [2] Func2, [3]Func3 - ");
    scanf("%d", &menu);

    if(menu < 0 || menu > 3)
      continue;
    if(menu == 0)
      break;
    funcArray[menu-1]();  //함수 실행
  }
}

void func1(void) {
  printf("func1 ex\n");
}
void func2(void) {
  printf("func2 ex\n");
}
void func3(void) {
  printf("func3 ex\n");
}
```
예제 4: typedef 사용, 함수 포인터 배열 사용
```
#include <stdio.h>
#pragma warning(disable:4996)

void func1(void);
void func2(void);
void func3(void);

typedef void (* func_t)(void);

int main(void) {
  func_t funcArray[3] = {func1, func2, func3 }; //함수 포인터 배열 선언, []갯수는 컴파일시 3 입력
  int menu;

  while(1) {
    printf("[0] Quit, [1] Func1, [2] Func2, [3]Func3 - ");
    scanf("%d", &menu);

    if(menu < 0 || menu > 3)
      continue;
    if(menu == 0)
      break;
    funcArray[menu-1]();  //함수 실행
  }
}

void func1(void) {
  printf("func1 ex\n");
}
void func2(void) {
  printf("func2 ex\n");
}
void func3(void) {
  printf("func3 ex\n");
}
```

### 1.13.2 함수 포인터를 구조체 멤버로 접근

* 구조체, 전역 함수 사용
```c
#include <stdio.h>
#pragma warning(disable:4996)

typedef struct _OBJECT {
  char model[32];
  int type;
  char size; //Small, Medium, Large
} OBJECT;

void inObject(OBJECT* pObj);
void outObject(const OBJECT* pObj);

int main(void) {
  OBJECT obj;

  inObject(&obj);
  outObject(&obj);

  return 0;
}

void inObject(OBJECT* pObj) {
  printf("[입력]\n");
  printf("Model: ");
  scanf("%s", pObj->model);

  printf("Type: ");
  scanf("%d", &pObj->type);  fflush(stdin);

  printf("Size: ");
  scanf("%c", &pObj->size);
}

void outObject(const OBJECT* pObj) {
  printf("[output]\n");
  printf("Model: %s\n", pObj->model);
  printf("Type: %d\n", pObj->type);
  printf("Size: %c\n", pObj->size);
}
```

* 구조체, 함수 포인터 멤버 사용
```c
#include <stdio.h>
#pragma warning(disable:4996)

typedef struct _OBJECT {
  char model[32];
  int type;
  char size; //Small, Medium, Large

  void (* inObject)(struct _OBJECT*);
  void (* outObject)(const struct _OBJECT*);
} OBJECT;

void initObject(OBJECT* pObj);

void inObject(OBJECT* pObj);
void outObject(const OBJECT* pObj);

int main(void) {
  OBJECT obj;

  initObject(&obj);

  obj.inObject(&obj);
  obj.outObject(&obj);

  return 0;
}
void initObject(OBJECT* pObj) {
  memset(pObj, 0, sizeof(OBJECT));

  pObj->inObject = inObject;
  pObj->outObject = outObject;
}

void inObject(OBJECT* pObj) {
  printf("[입력]\n");
  printf("Model: ");
  scanf("%s", pObj->model);

  printf("Type: ");
  scanf("%d", &pObj->type);  fflush(stdin);

  printf("Size: ");
  scanf("%c", &pObj->size);
}

void outObject(const OBJECT* pObj) {
  printf("[output]\n");
  printf("Model: %s\n", pObj->model);
  printf("Type: %d\n", pObj->type);
  printf("Size: %c\n", pObj->size);
}
```
### 1.13.3 함수 포인터 반환

* 함수포인터 반환, typedef 사용
```c
#include <stdio.h>
#pragma warning(disable:4996)

typedef void (* func_t)(void);
void func1(void);
void func2(void);
void func3(void);

func_t getFunctionPointer(int idx);

int main(void) {
  int menu;
  void (* func)(void) = NULL;

  while(1) {
    printf("[0] Quit, [1] Func1, [2] Func2, [3]Func3 - ");
    scanf("%d", &menu);

    if(menu < 0 || menu > 3)
      continue;
    if(menu == 0)
      break;

    func = getFunctionPointer(menu);
    func();
  }

  return 0;
}

void func1(void) {
  printf("func1 ex\n");
}
void func2(void) {
  printf("func2 ex\n");
}
void func3(void) {
  printf("func3 ex\n");
}

func_t getFunctionPointer(int idx) {
  void (* func)(void) = NULL;

  switch(idx) {
    case 1: func = func1; break;
    case 2: func = func2; break;
    case 3: func = func3; break;
  }
  return func;
}
```
* 구조체, 함수 포인터 배열 멤버 사용
```c
func_t getFunctionPointer(int idx) {
  void (* funcArray[])(void) = {func1, func2, func3};
  return funcArray[idx-1];
}
```
### 1.13.4 함수 포인터를 매개변수로 받는 함수
* 함수 포인터 매개변수
```c
#include <stdio.h>
#pragma warning(disable:4996)

void func1(void);
void proxyFunc(void (*func)(void));

int main(void) {
  int i;

  for(i == 0; i < 3; i++)
    proxyFunc(func1);

  return 0;
}

void func1(void) {
  printf("func1 ex\n");
}

void proxyFunc(void (* func)(void)) {
  func();
}
```

* 함수포인터를 매개변수로 받는 함수, typedef 사용
```c
typedef void (* func)(void);
void proxyFunc(func_t func);
```

* 매개 변수가 있는 함수 포인터 사용
```c
#include <stdio.h>
#include <stdlib.h>
#pragma warning(disable:4996)

typedef void (* func_t)(int*, int);

void func1(int* array, int size);
void func2(int* array, int size);
void func3(int* array, int size);

void proxyFunction(func_t func, int* array, int size);

int main(void) {
  int array[10];
  int menu;

  while(1) {
    printf("[0] Quit, [1] Func1, [2] Func2, [3]Func3 - ");
    scanf("%d", &menu);

    if(menu < 0 || menu > 3)
      continue;
    if(menu == 0)
      break;
    switch(menu) {
      case 1: proxyFunction(func1, array, 10); break;
      case 2: proxyFunction(func2, array, 10); break;
      case 3: proxyFunction(func3, array, 10); break;
    }
  }
}
void func1(int * array, int size) {
  int i;
  for(i=0; i < size; i++)
    array[i] = rand()%100;
}

void func2(int * array, int size) {
  int i;
  for(i = 0; i < size; i++)
    array[i] *= 2;
}

void func3(int * array, int size) {
  int i;
  for(i = 0; i < size; i++)
    printf("%3d", array[i]);
  printf("\n");
}

void proxyFunction(func_t func, int* array, int size) {
  func(array, size);
}
```
* 매개 변수가 있는 함수 포인터 배열 사용
```c
#include <stdio.h>
#include <stdlib.h>
#pragma warning(disable:4996)

void func1(int* pn);
void func2(int* pn);
void func3(int* pn);

void proxyFunction(void (* func)(int*), int* array, int size);

int main(void) {
  void (* funcArray[3])(int*) = {func1, func2, func3};

  int array[10] = {0};
  int menu;

  while(1) {
    printf("[0] Quit, [1] Func1, [2] Func2, [3]Func3 - ");
    scanf("%d", &menu);

    if(menu < 0 || menu > 3)
      continue;
    if(menu == 0)
      break;
    proxyFunction(funcArray[menu-1], array, 10);

    if(menu == 3)
      printf("\n");
  }
}
void func1(int *pn) {
  *pn = rand()%100;
}

void func2(int *pn) {
  *pn *= 2;
}

void func3(int *pn) {
  printf("%4d", *pn);
}

void proxyFunction(void (* func)(int*), int* array, int size) {
  int i;
  for(i = 0; i < size; i++)
    func(array+i);
}
```

### 1.13.5 quick sort 활용
* 구조체 배열을 Sorting
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#pragma warning(disable:4996)

enum { ARRAY_SIZE = 20};
enum {QUIT, TITLE, PRICE, PAGE, MENU_COUNT};

typedef struct {
  char title[32];
  int price;
  int page;
} BOOK;

void initBooks(BOOK* books, int size);
int selectMenu(void);
void printBooks(BOOK* books, int size);

int compareTitle(const void* p1, const void* p2);
int comparePrice(const void* p1, const void* p2);
int comparePage(const void* p1, const void* p2);

int main(void) {
  int (* compareFuncs[])(const void*, const void*) = {NULL, compareTitle, comparePrice, comparePage};
  BOOK books[ARRAY_SIZE];
  int select;

  initBooks(books, ARRAY_SIZE);

  printf("[정렬 이전]\n");
  printBooks(books, ARRAY_SIZE);

  while(1) {
    select = selectMenu();

    if(select == QUIT)
      break;
    qsort(books, ARRAY_SIZE, sizeof(books[0]), compareFuncs[select]);
    printBooks(books, ARRAY_SIZE);
  }
  return 0;
}

void initBooks(BOOK* books, int size) {
  BOOK books_1[3] =
  { "AAAA", 3200, 890,
    "BBBB", 1600, 425,
    "CCCC", 3400, 1074 };
  int i;
  for(i = 0; i < size; i++)
    books[i] = books_1[i%3];
}

int selectMenu(void) {
  char menu[32];
  int index = -1;

  do {
    printf("[Menu] quit, title, price, page -");
    scanf("%s", menu);
    fflush(stdin);

    if(strcmp(menu, "quit") == 0 ) index = QUIT;
    if(strcmp(menu, "title") == 0 ) index = TITLE;
    if(strcmp(menu, "price") == 0 ) index = PRICE;
    if(strcmp(menu, "page") == 0 ) index = PAGE;
  }
  while( index < 0 || index >= MENU_COUNT);

  return index;
}

void printBooks(BOOK* books, int size) {
  int i;
  for(i = 0; i < size; i++)
    printf("%-30s %5d %4d\n", books[i].title, books[i].price, books[i].page);
  printf("\n");
}

int compareTitle(const void* p1, const void* p2) {
  BOOK* pb1 = (BOOK*) p1, * pb2 = (BOOK*) p2;
  return strcmp(pb1->title, pb2->title);
}
int comparePrice(const void* p1, const void* p2){
  BOOK* pb1 = (BOOK*) p1, * pb2 = (BOOK*) p2;
  return (pb1->price - pb2->price);
}
int comparePage(const void* p1, const void* p2){
  BOOK* pb1 = (BOOK*) p1, * pb2 = (BOOK*) p2;
  return (pb1->page - pb2->page);
}
```
### 1.13.6 qsort(), bsearch() 활용
* 구조체 배열을 quick Sorting, 이진검색
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#pragma warning(disable:4996)

enum { ARRAY_SIZE = 20};

typedef struct {
  char title[32];
  int price;
  int page;
} BOOK;

void initBooks(BOOK* books, int size);
void printBooks(BOOK* books, int size);
int compareTitle(const void* p1, const void* p2);

int main(void) {
  BOOK books[ARRAY_SIZE];
  BOOK input;
  BOOK* find = NULL;

  initBooks(books, ARRAY_SIZE);

  qsort(books, ARRAY_SIZE, sizeof(books[0]), compareTitle);
  printBooks(books, ARRAY_SIZE);

  printf("[Exit] quit\n");

  while(1) {
    printf("제목 : ");
    gets(input.title); fflush(stdin);

    if(strcmp(input.title, "quit") == 0)
      break;

    find = bsearch(&input, books, ARRAY_SIZE, sizeof(books[0]), compareTitle);
    if(find != NULL)
      printf("Result : %s %d %d\n", find->title, find->price, find->page);
    else
      printf("Result : 없음\n");
  }
  return 0;
}

void initBooks(BOOK* books, int size) {
  BOOK books_1[3] =
  { "AAAA", 3200, 890,
    "BBBB", 1600, 425,
    "CCCC", 3400, 1074 };
  int i;
  for(i = 0; i < size; i++)
    books[i] = books_1[i%3];
}

void printBooks(BOOK* books, int size) {
  int i;
  for(i = 0; i < size; i++)
    printf("%-30s %5d %4d\n", books[i].title, books[i].price, books[i].page);
  printf("\n");
}

int compareTitle(const void* p1, const void* p2) {
  BOOK* pb1 = (BOOK*) p1, * pb2 = (BOOK*) p2;
  return strcmp(pb1->title, pb2->title);
}

```
