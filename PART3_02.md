🔹 3.2 스마트 포인터 (Smart Pointer)
📌 개요

C++에서는 new와 delete를 사용해 동적 메모리 할당을 직접 처리합니다.
하지만 이는 다음과 같은 자주 발생하는 문제를 유발합니다:

* 메모리 누수

* 이중 해제

* 예외 발생 시 리소스 누락

이를 해결하기 위해 C++11부터 스마트 포인터가 도입되었습니다.

✅ 스마트 포인터 주요 종류
| 종류                | 설명                               |
| ----------------- | -------------------------------- |
| `std::unique_ptr` | 소유권이 하나뿐인 포인터                    |
| `std::shared_ptr` | 참조 카운트를 사용하는 공유 포인터              |
| `std::weak_ptr`   | `shared_ptr`의 보조 포인터 (순환 참조 방지용) |

✅ 3.2.1 std::unique_ptr - 고유 소유권 포인터
기본 사용법
```Cpp
#include <iostream>
#include <memory>

int main() {
    std::unique_ptr<int> uptr = std::make_unique<int>(42);
    std::cout << *uptr << std::endl;

    // uptr2 = uptr;  // 복사 불가 (컴파일 에러)
    std::unique_ptr<int> uptr2 = std::move(uptr); // 이동만 가능

    if (!uptr) {
        std::cout << "uptr is now null.\n";
    }

    return 0;
}
```
특징

* 복사 금지 (deleted copy constructor)

* 이동 가능 (move semantics)

* 객체가 범위를 벗어나면 자동 delete

✅ 3.2.2 std::shared_ptr - 공유 소유권 포인터
기본 사용법
```Cpp
#include <iostream>
#include <memory>

int main() {
    std::shared_ptr<int> sp1 = std::make_shared<int>(100);
    std::shared_ptr<int> sp2 = sp1;  // 공유됨

    std::cout << "sp1 use_count: " << sp1.use_count() << std::endl;
    std::cout << "sp2 use_count: " << sp2.use_count() << std::endl;

    return 0;
}
```
특징

참조 카운트로 메모리 관리

마지막 shared_ptr 소멸 시 메모리 해제

다수 객체에서 안전하게 공유 가능

✅ 3.2.3 std::weak_ptr - 순환 참조 방지용 보조 포인터
순환 참조 문제 예시
```Cpp
#include <memory>

struct B;

struct A {
    std::shared_ptr<B> b_ptr;
};

struct B {
    std::shared_ptr<A> a_ptr;  // 순환 참조 발생
};
```
해결 방법: std::weak_ptr 사용
```Cpp
struct B;

struct A {
    std::shared_ptr<B> b_ptr;
};

struct B {
    std::weak_ptr<A> a_ptr;  // 약한 참조 → 순환 방지
};
```
특징

* shared_ptr이 아니므로 참조 카운트 증가시키지 않음

* expired() 또는 lock()을 통해 접근

✅ 3.2.4 스마트 포인터의 장단점
| 장점               | 단점                              |
| ---------------- | ------------------------------- |
| 메모리 자동 해제 (RAII) | 순환 참조 시 누수 위험 (→ `weak_ptr` 필요) |
| 예외 안전성 증가        | 약간의 성능 오버헤드                     |
| 명확한 소유권 개념       | 초기에는 문법이 어렵게 느껴질 수 있음           |

💡 스마트 포인터 정리 표
| 스마트 포인터      | 소유권 | 복사 가능 | 참조 카운트 | 특징             |
| ------------ | --- | ----- | ------ | -------------- |
| `unique_ptr` | 단독  | ❌     | ❌      | 가장 안전, 경량      |
| `shared_ptr` | 공유  | ✅     | ✅      | 복잡한 소유권 구조에 유용 |
| `weak_ptr`   | 없음  | ✅     | ❌      | 순환 참조 방지용      |

✅ 실제 객체에 사용 예
```Cpp
#include <iostream>
#include <memory>

class Person {
public:
    Person(std::string name) : name(name) {
        std::cout << name << " created.\n";
    }
    ~Person() {
        std::cout << name << " destroyed.\n";
    }

private:
    std::string name;
};

int main() {
    {
        std::shared_ptr<Person> p = std::make_shared<Person>("Alice");
    } // 범위 벗어나면 자동 소멸자 호출

    return 0;
}
```
