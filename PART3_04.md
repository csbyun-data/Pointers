## 3.4 C++ 동적 메모리 관리  
### 3.4.1 new / delete 연산자
* C에서의 malloc / free 대신, C++에서는 객체 생성 및 소멸에 맞춰 new / delete 연산자를 사용합니다.

* 기본 사용법
```cpp
int* ptr = new int;       // int 하나를 힙에 동적 할당
*ptr = 42;

delete ptr;               // 메모리 해제

배열 할당과 해제
int* arr = new int[5];    // int 배열 5개 동적 할당
delete[] arr;             // 배열은 delete[]로 해제해야 함!
```

⚠️ delete와 delete[]를 잘못 혼용하면 정의되지 않은 동작(UB)이 발생할 수 있습니다.

### 3.4.2 객체 배열 동적 할당
예제 1: 객체 배열 동적 할당
```cpp
#include <iostream>
using namespace std;

class Student {
public:
  Student() { cout << "Student created\n"; }
  ~Student() { cout << "Student destroyed\n"; }
};

int main() {
  Student* sArr = new Student[3];  // 생성자 3번 호출

  delete[] sArr;  // 소멸자 3번 호출
  return 0;
}
```
객체 배열의 경우 반드시 delete[]를 사용해야 모든 소멸자가 호출됩니다.

### 3.4.3 생성자와 소멸자 + 동적 메모리 관리
* 클래스 내부에서 동적 메모리를 할당할 경우, 생성자에서 할당하고 소멸자에서 해제하는 방식이 표준입니다.

예제 2: 문자열 복사 객체
```cpp
#include <iostream>
#include <cstring>
using namespace std;

class MyString {
  char* data;

public:
  MyString(const char* str) {
    data = new char[strlen(str) + 1];
    strcpy(data, str);
    cout << "Created: " << data << endl;
  }

  ~MyString() {
    cout << "Destroyed: " << data << endl;
    delete[] data;
  }

  void print() const {
    cout << data << endl;
  }
};

int main() {
  MyString s("Hello World");
  s.print();
  return 0;
}
```

* Rule of Three (세 가지 규칙)
클래스가 동적 메모리를 소유할 경우, 다음 세 가지를 모두 명시해야 안전합니다:
> 사용자 정의 소멸자 (~ClassName())
> 복사 생성자 (ClassName(const ClassName&))
> 복사 대입 연산자 (ClassName& operator=(const ClassName&))
✅ C++11 이상에서는 std::unique_ptr 등을 사용해 이 복잡성을 줄이는 것이 일반적입니다.

💡 동적 메모리 실수 방지 팁
| 실수 유형                  | 설명                     |
| ---------------------- | ---------------------- |
| `new[]` 했는데 `delete` 함 | 일부 소멸자 미호출 (메모리 누수 가능) |
| 이중 `delete`            | 프로그램 크래시 발생 가능         |
| `nullptr` 체크 없이 delete | 안전하긴 하지만 체크는 좋은 습관     |
| 할당 후 초기화 누락            | 예기치 않은 값 사용 위험         |

예제 3: 메모리 안전을 위한 모범 코드
```cpp
int* ptr = new int(10);

// ... 사용 후
if (ptr) {
    delete ptr;
    ptr = nullptr;  // dangling pointer 방지
}
```
