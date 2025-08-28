🔹 3.6 C++ 표준 라이브러리와 포인터 활용
✅ 3.6.1 std::vector와 동적 배열
📌 기본 개념

std::vector는 내부적으로 **동적 배열(heap)**을 사용하며,
자동 크기 조절, 메모리 관리, RAII가 적용된 컨테이너입니다.

예제: 동적 배열 대신 vector 사용
```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    
    vec.push_back(6);  // 자동으로 크기 증가
    vec[0] = 10;

    for (int val : vec) {
        cout << val << " ";
    }
    return 0;
}
```
✅ 3.6.2 포인터와 반복자(Iterator)
📌 반복자는 포인터처럼 동작하는 STL 개념

vector<int>::iterator는 포인터처럼 동작하는 객체로,
+1 연산, 역참조(*), 비교(==, !=) 등 포인터 연산이 가능

예제: 반복자 사용
```cpp
vector<int> v = {10, 20, 30, 40};

for (vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
    cout << *it << " ";  // 마치 포인터처럼 사용
}
```
반복자 = 추상화된 포인터 개념

→ 다양한 STL 컨테이너에 사용 가능 (list, set, map 등)

✅ 3.6.3 기타 STL 컨테이너와 포인터 관계
📌 컨테이너 내부에 포인터 저장 가능
```cpp
vector<int*> ptrs;

int a = 10, b = 20;
ptrs.push_back(&a);
ptrs.push_back(&b);

for (int* p : ptrs) {
    cout << *p << " ";
}
```

→ 객체를 직접 저장할 수도 있고,
→ 객체에 대한 포인터를 저장해 공유하거나 다형성을 구현할 수도 있음.

✅ 3.6.4 Raw Pointer vs Iterator 비교
| 항목     | Raw Pointer | STL Iterator                      |
| ------ | ----------- | --------------------------------- |
| 타입 안정성 | 낮음          | 높음                                |
| 범위 체크  | 불가능         | 일부 지원 (`at()`, `bounds checking`) |
| 추상화    | 없음          | 있음 (컨테이너 종류 무관하게 사용 가능)           |
| 예외 안전성 | 낮음          | 높음                                |
| 가독성    | 낮을 수 있음     | 높음 (C++ 스타일)                      |

✅ 예시 비교
```cpp
int arr[3] = {1, 2, 3};
int* p = arr;

while (p != arr + 3) {
    cout << *p << " ";
    ++p;
}
```

vs.
```cpp
vector<int> v = {1, 2, 3};
for (auto it = v.begin(); it != v.end(); ++it) {
    cout << *it << " ";
}
```
📌 결론 요약
| 개념                      | 설명                        |
| ----------------------- | ------------------------- |
| `vector`                | 포인터 없이 안전하게 동적 배열 관리 가능   |
| 반복자                     | 포인터 추상화 → 범용적, STL 전반에 사용 |
| 포인터 저장 컨테이너             | 다형성 및 리소스 공유에 유용          |
| Raw Pointer vs Iterator | 반복자는 타입 안정성과 추상화 제공       |
