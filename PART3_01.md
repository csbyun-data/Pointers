## 3.1 C++ 참조자 (Reference) 개념
참조자란? C++에서 참조자(reference)는 **다른 변수의 별명(alias)**입니다.
포인터처럼 메모리 주소를 직접 다루지 않고도, 원본 데이터를 수정하거나 참조할 수 있게 해줍니다.

```cpp
int a = 10;
int& ref = a;  // ref는 a의 별명

ref = 20;      // a도 20으로 바뀜
```
### 3.1.1 참조자 선언과 초기화

예제 1: 참조자 선언과 초기화
```cpp
#include <iostream>
using namespace std;

int main() {
    int x = 5;
    int& ref = x;   // ref는 x를 참조

    cout << "x: " << x << ", ref: " << ref << endl;

    ref = 10;       // x도 바뀜
    cout << "x: " << x << ", ref: " << ref << endl;

    return 0;
}
```

✔ 실행 결과
```
x: 5, ref: 5  
x: 10, ref: 10
```

* 초기화 시점에 반드시 참조할 대상을 지정해야 하며, 한 번 초기화된 후에는 다른 변수로 참조 변경 불가입니다.

### 3.1.2 참조자와 포인터의 차이점
| 항목         | 참조자 (Reference)      | 포인터 (Pointer)      |
| ---------- | -------------------- | ------------------ |
| 선언 형식      | `int& ref = var;`    | `int* ptr = &var;` |
| NULL 가능 여부 | ❌ 불가능 (항상 유효한 변수 참조) | ✅ 가능               |
| 변경 가능 여부   | ❌ 참조 대상 변경 불가        | ✅ 다른 주소로 변경 가능     |
| 사용 시 문법    | 변수처럼 사용              | `*ptr`, `&var` 필요  |
| 메모리 사용     | 실제 별명 (간접적)          | 주소값 저장 변수          |

예시 비교: 참조자와 포인터
```cpp
int a = 5, b = 10;
int& ref = a;
ref = b;      // a = b; 와 같음 (참조 대상 변경 X)

int* ptr = &a;
ptr = &b;     // 포인터 대상 변경 가능
```

### 3.1.3 함수 매개변수로서의 참조자 (Call by Reference)
* C++에서는 참조자를 함수 인자로 사용하여 원본 수정이 가능합니다.

예제 2: 값 전달 vs 참조 전달
```cpp
void modify_by_value(int n) {
    n = 100;
}

void modify_by_reference(int& n) {
    n = 100;
}

int main() {
    int x = 10;

    modify_by_value(x);       // x remains 10
    cout << "After by value: " << x << endl;

    modify_by_reference(x);   // x becomes 100
    cout << "After by reference: " << x << endl;

    return 0;
}
```

✔ 실행 결과:
```
After by value: 10  
After by reference: 100
```

### 3.1.4 참조자의 한계와 제약
| 제한 사항          | 설명                           |
| -------------- | ---------------------------- |
| 참조는 NULL일 수 없음 | 참조는 항상 유효한 대상을 가져야 함         |
| 초기화는 반드시 필요    | 선언과 동시에 참조할 변수 필요            |
| 참조 대상 변경 불가    | 한번 연결된 대상은 끝까지 유지됨           |
| 참조 배열 불가       | 참조자의 배열 선언은 불가능 (포인터 배열로 대체) |

⚠️ 특이한 예시: 반환값으로 참조 사용
```cpp
int& get_element(int arr[], int index) {
    return arr[index]; // 원본 배열 요소 참조 반환
}

int main() {
    int arr[5] = {1, 2, 3, 4, 5};
    get_element(arr, 2) = 100; // arr[2] = 100;

    cout << arr[2] << endl; // 100
}
```
