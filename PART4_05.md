🔹 4.5 Dangling 포인터와 Wild 포인터  
✅ 1. Dangling Pointer (댕글링 포인터)
📌 정의

이미 해제된 메모리를 계속 가리키고 있는 포인터

예시
```cpp
int* ptr = new int(10);
delete ptr;
*ptr = 20;  // ❌ dangling pointer 사용 → 정의되지 않은 동작
```

이 상황에서 ptr은 아직 메모리를 가리키는 것처럼 보이지만,
실제로는 그 메모리가 OS에게 반환된 상태입니다.

✅ 2. Wild Pointer (와일드 포인터)  
📌 정의

초기화되지 않은 포인터 또는 임의의 주소를 가리키는 포인터
```cpp
int* ptr;    // ❌ 초기화 안 됨
*ptr = 5;    // 메모리 오류 가능성 매우 높음
```

또는:
```cpp
int* ptr = (int*)0x12345678;  // ❌ 의미 없는 주소를 가리킴
```

Wild pointer는 메모리 보호 오류(segfault)를 유발할 수 있으며,
외부 공격자가 악용할 가능성이 있는 보안 리스크입니다.

✅ 차이점 비교
| 항목    | Dangling Pointer           | Wild Pointer                    |
| ----- | -------------------------- | ------------------------------- |
| 발생 시점 | 메모리 해제 후                   | 초기화 없이 사용                       |
| 상태    | 유효했던 주소 → 무효화됨             | 처음부터 유효하지 않음                    |
| 대표 예시 | `delete` 후 포인터 사용          | 포인터 선언 후 바로 사용                  |
| 위험도   | 높음 (데이터 훼손, 예외)            | 높음 (segfault, 악성 주소 접근)         |
| 해결 방법 | `nullptr`로 초기화, 해제 후 사용 금지 | 포인터 선언 즉시 `nullptr` 또는 유효 주소 할당 |

✅ 실수 방지 습관

1. 포인터 선언 시 즉시 초기화
```cpp
int* p = nullptr;
```

2. delete 후 nullptr로 설정
```cpp
delete p;
p = nullptr;
```

3. 포인터 사용 전 유효성 확인
```cpp
if (p != nullptr) {
    *p = 10;
}
```

4. 스마트 포인터 사용 권장
```cpp
std::unique_ptr<int> p = std::make_unique<int>(100);
// 자동 메모리 관리 → dangling, wild 방지
```
✅ 예시: 잘못된 vs 안전한 코드  
❌ 잘못된 코드
```cpp
int* p;
*p = 10;  // wild pointer → 오류
```
✅ 안전한 코드
```cpp
int* p = nullptr;
if (p) {
    *p = 10;
}
```
📌 요약
| 구분  | Dangling Pointer          | Wild Pointer       |
| --- | ------------------------- | ------------------ |
| 정의  | 해제된 메모리를 가리킴              | 초기화되지 않거나 임의 주소 사용 |
| 문제점 | 정의되지 않은 동작, 크래시           | 크래시, 메모리 훼손, 보안 문제 |
| 해결책 | delete 후 nullptr, 스마트 포인터 | 선언 시 초기화, 유효성 검사   |
