## 포인터 복습 문제 10선  
✅ [기초 문제]  
🔹 문제 1. 포인터 선언과 사용

다음 코드에서 출력 결과를 쓰시오.
```c
int x = 10;
int* ptr = &x;
printf("%d\n", *ptr);
```
🔹 문제 2. 포인터를 사용한 값 교환

다음 swap() 함수가 두 변수 값을 정확히 교환하도록 빈칸을 채우시오.
```c
void swap(______, ______) {
    int temp = *a;
    *a = *b;
    *b = temp;
}
```
🔹 문제 3. 포인터와 배열

다음 코드에서 *(arr + 2)가 가리키는 값은?
```c
int arr[] = {5, 10, 15, 20};
```
🔹 문제 4. 문자열 포인터

다음 중 문자열 복사에 문제가 있는 코드는?

a)
```c
char str1[10];
char* str2 = "Hello";
strcpy(str1, str2);
```

b)
```c
char* str1;
char* str2 = "Hello";
strcpy(str1, str2);
```
✅ [중급 문제]  
🔹 문제 5. 이중 포인터 사용

다음 중 int** 타입 포인터를 제대로 사용하는 코드를 모두 고르시오.

a)
```c
int a = 5;
int* p = &a;
int** pp = &p;
```

b)
```c
int a = 5;
int** pp = &a;
```

c)
```c
int* p;
int** pp = &p;
```
🔹 문제 6. 포인터 연산

다음 코드에서 출력 결과는?
```c
int arr[] = {1, 2, 3, 4};
int* p = arr;
p += 2;
printf("%d", *p);
```
🔹 문제 7. 동적 메모리 할당

다음 코드에서 메모리 누수의 원인을 설명하시오.
```c
int* p = (int*)malloc(sizeof(int) * 10);
p = (int*)malloc(sizeof(int) * 20);
```
🔹 문제 8. 구조체 포인터 접근

다음 중 구조체 포인터에서 name 멤버에 접근하는 올바른 문장은?
```c
struct Student {
    char name[20];
    int score;
};
Student* s;
```

a) (*s).name = "Kim";
b) s->name = "Kim";
c) strcpy(s->name, "Kim");

✅ [응용 문제]
🔹 문제 9. 연결 리스트 노드 삽입

아래 insert() 함수는 단순 연결 리스트에 새로운 노드를 삽입한다.
빈칸을 채우시오.
```c
struct Node {
    int data;
    Node* next;
};

void insert(Node** head, int value) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->data = value;
    newNode->next = _______;
    *head = _______;
}
```
🔹 문제 10. 스마트 포인터

다음 중 unique_ptr의 특징으로 옳지 않은 것은?

a) 복사가 가능하다  
b) std::move()를 사용해 소유권 이전이 가능하다  
c) delete를 사용하지 않아도 자동으로 메모리가 해제된다  
d) STL 컨테이너와 함께 사용할 수 있다
