🔹 3.3 함수 포인터와 함수 객체 (Functors)  
✅ 3.3.1 함수 포인터 정의와 활용  
📌 함수 포인터 기본 개념  

C/C++에서는 함수도 메모리에 위치하고 있으므로,
함수의 주소를 변수처럼 저장하고 호출할 수 있습니다.

예제 1: 기본 함수 포인터 사용
```cpp
#include <iostream>

void greet() {
    std::cout << "Hello from function!\n";
}

int main() {
    void (*func_ptr)() = greet;  // 함수 포인터 선언 및 초기화
    func_ptr();                  // 포인터로 함수 호출
    return 0;
}
```
예제 2: 매개변수와 반환값 포함
```cpp
int add(int a, int b) {
    return a + b;
}

int main() {
    int (*op)(int, int) = add;
    std::cout << op(3, 4) << std::endl;  // 출력: 7
    return 0;
}
```
함수 포인터 문법 요약
```cpp
반환형 (*포인터이름)(매개변수들)
```
✅ 3.3.2 콜백 함수 구현

함수 포인터는 콜백(callback) 함수 구현에 많이 사용됩니다.
→ 함수를 인자로 전달하여 동작을 유연하게 바꾸는 구조

예제: 조건에 따라 다른 함수를 호출
```cpp
#include <iostream>

void log_info(const std::string& msg) {
    std::cout << "[INFO]: " << msg << std::endl;
}

void log_error(const std::string& msg) {
    std::cout << "[ERROR]: " << msg << std::endl;
}

void process(void (*logger)(const std::string&), const std::string& msg) {
    logger(msg);  // 전달된 함수 호출
}

int main() {
    process(log_info, "All systems go.");
    process(log_error, "Something went wrong!");
    return 0;
}
```
✅ 3.3.3 C++ 함수 객체 (Functor) 개념 및 구현

함수 객체(Functor)는 함수처럼 동작하는 객체입니다.
→ operator()를 정의한 클래스

예제: Functor 정의
```cpp
#include <iostream>

class Multiply {
public:
    int operator()(int a, int b) {
        return a * b;
    }
};

int main() {
    Multiply mul;
    std::cout << mul(3, 4) << std::endl;  // 출력: 12
    return 0;
}
```
장점

* 상태(state)를 가질 수 있다.

* 템플릿, STL과 잘 어울린다 (e.g., std::sort)

✅ 3.3.4 람다 표현식 소개

람다(lambda)는 C++11부터 도입된 익명 함수입니다.
→ 코드가 더 간결해지고, 함수 포인터나 Functor를 대체 가능

기본 문법
```cpp
[capture](parameters) -> return_type { body }
```
예제
```cpp
#include <iostream>

int main() {
    auto add = [](int a, int b) -> int {
        return a + b;
    };

    std::cout << add(5, 7) << std::endl;  // 출력: 12
    return 0;
}
```
✅ 3.3.5 람다와 캡쳐 방식

람다는 외부 변수를 캡쳐(capture) 하여 사용할 수 있습니다.

| 문법        | 의미                |
| --------- | ----------------- |
| `[=]`     | 외부 변수를 **값**으로 캡쳐 |
| `[&]`     | 외부 변수를 **참조**로 캡쳐 |
| `[x]`     | x만 캡쳐             |
| `[=, &y]` | x는 값으로, y는 참조로 캡쳐 |

예제
```cpp
int x = 10, y = 5;

auto f1 = [=]() { return x + y; };  // 값 캡쳐
auto f2 = [&]() { x++; return x + y; };  // 참조 캡쳐
```
💡 함수 객체, 포인터, 람다 비교 요약
| 형태      | 장점                | 단점                  |
| ------- | ----------------- | ------------------- |
| 함수 포인터  | 간단, 오래된 C 코드와 호환  | 상태 저장 불가, 타입 안전성 낮음 |
| Functor | 상태 저장 가능, 템플릿에 강함 | 구현 번거로움             |
| 람다      | 간결, 가독성 좋음, 캡쳐 가능 | 복잡한 람다는 디버깅 어려움     |
