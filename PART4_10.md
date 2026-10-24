## 4.10 스마트 포인터 사용 시 주의점 (C++ 파트 연계)  
### 4.10.1 스마트 포인터란?
  C++11부터 도입된 RAII 기반 포인터 래퍼로,  
  객체가 더 이상 필요 없을 때 자동으로 메모리를 해제해줍니다.

* std::unique_ptr
* std::shared_ptr
* std::weak_ptr

### 4.10.2 unique_ptr 관련 주의점
```cpp
std::unique_ptr<int> p1 = std::make_unique<int>(10);
std::unique_ptr<int> p2 = p1;  // ❌ 복사 불가
```

* unique_ptr는 소유권이 단 하나여야 하므로 복사 불가능
* 소유권 이전은 std::move()를 통해서만 가능
```cpp
std::unique_ptr<int> p2 = std::move(p1);  // ✅ OK
```

🔸 주의: std::move() 후 원래 포인터는 nullptr 상태가 되므로 사용 금지

### 4.10.3 shared_ptr 순환 참조 문제
```cpp
struct Node {
  std::shared_ptr<Node> next;
};

std::shared_ptr<Node> a = std::make_shared<Node>();
std::shared_ptr<Node> b = std::make_shared<Node>();

a->next = b;
b->next = a;  // ❌ 순환 참조 발생
```

* shared_ptr은 참조 카운트를 기반으로 삭제되므로
서로가 서로를 가리키면 참조 카운트가 0이 되지 않아 메모리 누수 발생

* 해결 방법: weak_ptr로 끊어주기
```cpp
struct Node {
    std::weak_ptr<Node> next;  // 🔁 끊어주기
};
```
### 4.10.4 delete와 병행 사용 금지
``` cpp
std::shared_ptr<int> p(new int(100));
delete p.get();  // ❌ 절대 하지 말 것
```

* 스마트 포인터는 자체적으로 소멸자를 호출하므로,
delete를 수동으로 호출하면 이중 해제(double delete) 발생

항상 delete는 직접 사용하지 말고 스마트 포인터에 맡길 것.

### 4.10.5 원시 포인터(raw pointer) 노출 주의
```cpp
int* raw = p.get();  // ✅ 가능하나...
*raw = 10;
// 이후 raw를 delete 하면 ❌ 위험
```

* .get()은 내부 포인터를 꺼내는 함수지만 원시 포인터를 외부에서 관리하지 말 것

👉 가능하면 .get() 사용도 최소화하고 스마트 포인터 내부에서만 처리

### 4.10.6 컨테이너와 함께 쓸 때 소유권 명확히 하기
```cpp
std::vector<std::shared_ptr<Foo>> v;
```

* 컨테이너에 shared_ptr을 저장할 경우, 해당 객체가 언제 삭제될지 명확히 파악 필요

* unique_ptr과 함께 사용할 경우 move semantics 필수
```cpp
std::vector<std::unique_ptr<Foo>> v;
v.push_back(std::make_unique<Foo>());  // ✅ OK
```

### 4.10.6 요약: 스마트 포인터 사용 시 주의사항 정리
| 항목                    | 주의사항 설명                |
| --------------------- | ---------------------- |
| `unique_ptr` 복사 금지    | 반드시 `std::move()` 사용   |
| `shared_ptr` 순환 참조 주의 | `weak_ptr`로 끊어야 함      |
| `delete` 직접 호출 금지     | 스마트 포인터가 소멸자 호출        |
| `.get()` 남용 금지        | 원시 포인터 노출 자제           |
| 컨테이너 사용 시 소유권 명확화     | `move`, RAII 원칙 철저히 적용 |
