## 4.3 이중 해제 문제 (Double Free)  
  delete 또는 free를 통해 해제된 메모리를
  한 번 더 해제하려고 시도할 경우 발생하는 오류입니다.

  이중 해제는 C/C++에서 매우 치명적인 **정의되지 않은 동작(UB)**을 유발합니다.

### 4.3.1 이중 해제 발생
```cpp
int* ptr = new int(10);
delete ptr;
delete ptr;  // ❌ 이중 해제 → 정의되지 않은 동작
```
결과:

* 크래시, 이상 동작, 해커에게 공격 지점 제공 (heap overflow 악용 가능)

### 4.3.2 왜 위험한가?

* 메모리 할당 라이브러리는 내부적으로 **프리 리스트(free list)**를 관리함

* 이중 해제로 인해 이미 해제된 메모리 영역이 다시 해제되면서, 힙 구조가 손상됨 (heap corruption)

### 4.3.3 안전한 코드 패턴: nullptr로 초기화
```cpp
int* ptr = new int(20);
delete ptr;
ptr = nullptr;  // 이후 delete해도 안전

delete ptr;  // ✅ 아무 일도 일어나지 않음 (nullptr는 안전)

delete nullptr;는 표준적으로 허용됨
int* p = nullptr;
delete p;  // ✅ OK
```

### 4.3.4 실수 유형 예  
❌ 예 1: 두 포인터가 같은 메모리를 가리킬 때
```cpp
int* a = new int(5);
int* b = a;

delete a;
delete b;  // ❌ 이중 해제
```
* 해결: 하나만 소유하거나 스마트 포인터 사용  
* 해결 방법
| 상황              | 해결 방안                                   |
| --------------- | --------------------------------------- |
| 포인터 해제 후 사용 금지  | `nullptr`로 초기화                          |
| 여러 포인터가 하나를 가리킴 | 스마트 포인터 (`shared_ptr`, `unique_ptr`) 사용 |
| 복사 방지 필요 시      | 소유권 이전(move semantics) 적용               |

* 스마트 포인터로 안전하게
```cpp
#include <memory>

void example() {
  std::shared_ptr<int> sp1 = std::make_shared<int>(42);
  std::shared_ptr<int> sp2 = sp1;  // 참조 카운트 증가

  // 자동으로 참조 카운트 관리 → double free 방지
}
```

스마트 포인터는 이중 해제를 자동으로 방지해 줍니다.

### 4.3.5 요약
| 항목      | 설명                                            |
| ------- | --------------------------------------------- |
| 이중 해제란? | 같은 포인터를 두 번 이상 `delete`하거나 `free`하는 것         |
| 결과      | 프로그램 크래시, 힙 손상, 보안 문제                         |
| 방지법     | delete 후 `nullptr` 대입, 소유권 명확히 구분, 스마트 포인터 사용 |
| 팁       | `delete nullptr`는 안전하므로 초기화 습관화 필요            |
