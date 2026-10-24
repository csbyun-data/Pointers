## 부록 B: 실습 문제 및 예제 코드  
### B.1 기본 포인터 연습  
* 문제 1: 포인터를 사용한 값 교환 함수
```cpp
void swap(int* a, int* b);

// main 함수 예제
int x = 10, y = 20;
swap(&x, &y);
std::cout << "x=" << x << ", y=" << y << std::endl;
// 결과: x=20, y=10
```
* 포인터를 통해 함수 내에서 원본 변수 값을 변경, Call by Reference 유사 구현

### B.2 동적 메모리 할당  
* 문제 2: 사용자 입력으로 정수 N개를 저장하고 평균 계산
```cpp
int n;
std::cin >> n;

int* arr = new int[n];

for (int i = 0; i < n; ++i)
    std::cin >> arr[i];

// 평균 계산
double sum = 0;
for (int i = 0; i < n; ++i)
    sum += arr[i];

std::cout << "Average: " << sum / n << std::endl;

delete[] arr;
```
* new[] / delete[] 연습, 유효성 검사 추가 가능

### B.3 문자열과 포인터
* 문제 3: strlen() 함수 직접 구현
```cpp
int my_strlen(const char* str) {
    int len = 0;
    while (*str++) ++len;
    return len;
}
```
* 문자열 종료 조건 '\0'까지 반복, const char* 사용

### B.4 이중 포인터  
* 문제 4: 이중 포인터로 배열 생성 및 해제
```cpp
int rows = 3, cols = 4;
int** matrix = new int*[rows];

for (int i = 0; i < rows; ++i)
    matrix[i] = new int[cols];

// 값 할당 및 출력
for (int i = 0; i < rows; ++i)
    for (int j = 0; j < cols; ++j)
        matrix[i][j] = i * cols + j;

// 메모리 해제
for (int i = 0; i < rows; ++i)
    delete[] matrix[i];
delete[] matrix;
```

* 동적 2차원 배열 구조, 메모리 해제 순서 중요

### B.5 구조체와 포인터
* 문제 5: 구조체 포인터를 사용한 멤버 접근
```cpp
struct Student {
    char name[20];
    int score;
};

Student* s = new Student;
strcpy(s->name, "Alice");
s->score = 95;

std::cout << s->name << " " << s->score << std::endl;

delete s;
```
* -> 연산자 사용, 동적 구조체 할당/해제

### B.6 연결 리스트 구현  
* 문제 6: 단순 연결 리스트 노드 삽입
```cpp
struct Node {
    int data;
    Node* next;
};

void insert(Node** head, int value) {
    Node* newNode = new Node{value, *head};
    *head = newNode;
}
```
* 이중 포인터로 head 자체를 수정. 동적 노드 생성 및 연결

### B.7 스마트 포인터 실습 (C++11 이상)  
* 문제 7: unique_ptr로 객체 관리
```cpp
#include <memory>

class MyClass {
public:
    MyClass() { std::cout << "Created\n"; }
    ~MyClass() { std::cout << "Destroyed\n"; }
};

int main() {
    std::unique_ptr<MyClass> ptr = std::make_unique<MyClass>();
}
```
* 소유권 자동 관리. 소멸자 호출 자동 처리 확인

### B.8 보너스 문제: 포인터 배열 vs 배열 포인터  
* 문제 8: 차이 이해하기
```cpp
int* arr1[3];        // 포인터 3개
int (*arr2)[3];      // int 3개 배열을 가리키는 포인터


arr1[i] = new int; 형태 가능

arr2 = new int[1][3];처럼 사용
```
### B.9 마무리 팁
* 각 문제는 디버거로 실행해보며 포인터의 주소와 값을 확인하면 효과적입니다.
* 메모리 할당 후 nullptr 체크 및 delete 누락 여부 항상 점검하세요.

* STL 사용이 가능할 경우 vector, unique_ptr 등으로 안전하게 대체하는 연습도 병행해 보세요.
