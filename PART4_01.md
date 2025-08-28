🔹 4.1 초기화 누락 문제  
✅ 개요

C/C++에서 포인터를 선언만 하고 초기화하지 않으면,
해당 포인터는 임의의 메모리 주소를 가리키게 됩니다.
→ 이를 역참조하면 예기치 않은 동작, 세그멘테이션 오류(Segmentation Fault) 발생

✅ 잘못된 예: 초기화하지 않은 포인터
```cpp
#include <iostream>

int main() {
    int* p;        // 초기화하지 않음
    *p = 100;      // ❌ 정의되지 않은 동작 (쓰레기 주소 접근)
    return 0;
}
```
출력 (또는 실행 결과):
```
Segmentation fault (core dumped)
```
✅ 올바른 사용 예 ①: 명시적 초기화
```cpp
int* p = nullptr;   // 안전한 초기화

if (p) {
    *p = 100;       // 실행되지 않음
}
```

nullptr은 어떤 유효한 메모리도 가리키지 않음을 명시

✅ 올바른 사용 예 ②: 유효한 주소로 초기화
```cpp
int a = 42;
int* p = &a;

std::cout << *p << std::endl;  // 출력: 42
````
✅ 실수 방지 요령
| 상황              | 대처 방법                       |
| --------------- | --------------------------- |
| 포인터 선언          | 항상 `nullptr` 또는 유효한 주소로 초기화 |
| 함수 매개변수로 포인터 전달 | 함수 내에서 `nullptr` 체크 필수      |
| 구조체/클래스 멤버 포인터  | 생성자에서 명시적으로 초기화             |

🧠 팁: C++에서는 nullptr 사용 권장

* C의 NULL은 단순히 0이라서 혼란 발생 가능

* C++11 이상에서는 nullptr을 사용해 타입 안전한 null 포인터 표현

✅ 예제: 함수에서 안전한 포인터 처리
```cpp
void printValue(int* ptr) {
    if (ptr != nullptr) {
        std::cout << "Value: " << *ptr << std::endl;
    } else {
        std::cout << "Null pointer passed.\n";
    }
}

int main() {
    int* p = nullptr;
    printValue(p);  // 안전
}
```
📌 요약
| 항목 | 설명                         |
| -- | -------------------------- |
| 문제 | 포인터를 초기화하지 않으면 쓰레기 값 보유    |
| 결과 | 프로그램 크래시, 이상 동작, 보안 취약점 유발 |
| 해결 | `nullptr` 초기화 또는 유효한 주소 부여 |
| 팁  | 항상 null 체크, 특히 함수 인자에서 중요  |
