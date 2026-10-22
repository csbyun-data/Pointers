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
