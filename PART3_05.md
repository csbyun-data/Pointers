## 3.5 클래스와 포인터
### 3.5.1 클래스 멤버로서 포인터
* 클래스 멤버로 포인터를 선언하면, 객체 생성 시 포인터가 가리키는 메모리를 동적으로 할당하고 직접 관리해야 합니다.

예제 1: 포인터 멤버를 가진 클래스
```cpp
#include <iostream>
#include <cstring>
using namespace std;

class Person {
  char* name;

public:
  Person(const char* n) {
    name = new char[strlen(n) + 1];
    strcpy(name, n);
  }

  void print() {
    cout << "Name: " << name << endl;
  }

  ~Person() {
    delete[] name;
  }
};

int main() {
  Person p("Alice");
  p.print();
  return 0;
}
```
📌 이 경우 Rule of Three를 적용해야 안전하게 복사/대입 가능함.

### 3.5.2 동적 할당을 포함한 클래스 디자인
예제 2: 복사 생성자와 대입 연산자 정의
```cpp
class Person {
  char* name;

public:
  Person(const char* n) {
    name = new char[strlen(n) + 1];
    strcpy(name, n);
  }

  // 복사 생성자
  Person(const Person& other) {
    name = new char[strlen(other.name) + 1];
    strcpy(name, other.name);
  }

  // 대입 연산자
  Person& operator=(const Person& other) {
    if (this != &other) {
      delete[] name;
      name = new char[strlen(other.name) + 1];
      strcpy(name, other.name);
    }
    return *this;
  }

  ~Person() {
    delete[] name;
  }

  void print() {
    cout << "Name: " << name << endl;
  }
};
```

* 이 세 가지 함수(소멸자, 복사 생성자, 대입 연산자)를 모두 정의하면
클래스가 포인터를 안전하게 다룰 수 있습니다.
이를 Rule of Three라고 합니다.

### 3.5.3 RAII 원칙과 스마트 포인터 활용
* RAII란? Resource Acquisition Is Initialization
자원(메모리, 파일 등)을 객체 생성 시점에 획득하고, 소멸 시점에 자동 해제하는 C++ 객체 관리 원칙

예제 3: RAII 예(unique_ptr을 활용한 클래스)
```cpp
#include <iostream>
#include <memory>
using namespace std;

class Person {
  unique_ptr<string> name;

public:
  Person(const string& n) : name(make_unique<string>(n)) {}

  void print() {
    cout << "Name: " << *name << endl;
  }
};

int main() {
  Person p("Bob");
  p.print();

  return 0;
}
```
📌 unique_ptr은 복사 불가능하므로, 복사 생성자/대입 연산자를 명시하거나
shared_ptr 등으로 변경해야 공유 가능.

📚 RAII vs 수동 메모리 관리 비교
| 항목     | 수동 관리 (`new`/`delete`) | RAII (`unique_ptr`, `vector` 등) |
| ------ | ---------------------- | ------------------------------- |
| 실수 가능성 | 높음 (누수, 이중 해제 등)       | 낮음 (자동 해제)                      |
| 코드 복잡도 | 높음                     | 낮음                              |
| 예외 안전성 | 낮음                     | 높음                              |
| 유지보수성  | 낮음                     | 높음                              |

📚 정리 요약
| 개념            | 설명                                    |
| ------------- | ------------------------------------- |
| 클래스 내부 포인터    | 반드시 생성자/소멸자에서 메모리 관리 필요               |
| Rule of Three | 동적 메모리 사용 클래스는 소멸자, 복사 생성자, 대입 연산자 필수 |
| 스마트 포인터       | `unique_ptr`로 소유권 관리, RAII 적용         |
| RAII 원칙       | 객체 생명주기 = 자원 생명주기. 누수 방지              |
