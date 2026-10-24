## 부록 A: 포인터 관련 주요 함수 요약  
### A.1 메모리 관련 함수 (C 표준 라이브러리)
| 함수 이름       | 헤더           | 설명                  |
| ----------- | ------------ | ------------------- |
| `malloc()`  | `<stdlib.h>` | 동적 메모리 할당 (초기화 없음)  |
| `calloc()`  | `<stdlib.h>` | 동적 메모리 할당 + 0으로 초기화 |
| `realloc()` | `<stdlib.h>` | 할당된 메모리 크기 재조정      |
| `free()`    | `<stdlib.h>` | 동적 메모리 해제           |

💡 예제
```c
int* arr = (int*)malloc(sizeof(int) * 5);
arr[0] = 42;
free(arr);
```
### A.2 문자열 관련 함수 (포인터와 밀접)
| 함수 이름       | 헤더           | 설명             |
| ----------- | ------------ | -------------- |
| `strlen()`  | `<string.h>` | 문자열 길이 반환      |
| `strcpy()`  | `<string.h>` | 문자열 복사         |
| `strncpy()` | `<string.h>` | 지정 길이만큼 문자열 복사 |
| `strcmp()`  | `<string.h>` | 문자열 비교         |
| `strcat()`  | `<string.h>` | 문자열 이어 붙이기     |

💡 예제
```c
char str1[20] = "Hello, ";
char str2[] = "World!";
strcat(str1, str2);  // str1에 str2를 붙임
```

### A.3 포인터 비교 및 NULL 체크
| 용도      | 예제 코드                             | 설명                  |
| ------- | --------------------------------- | ------------------- |
| NULL 체크 | `if (ptr == NULL)` 또는 `if (!ptr)` | 포인터가 유효한지 확인        |
| 포인터 비교  | `if (p1 == p2)`                   | 두 포인터가 같은 주소를 가리키는지 |

### A.4 함수 포인터 관련 함수
| 예제 함수 선언                | 설명                  |
| ----------------------- | ------------------- |
| `void (*fptr)(int);`    | int 매개변수를 받는 함수 포인터 |
| `fptr = &someFunction;` | 함수 주소 대입            |
| `fptr(10);`             | 함수 호출               |

💡 예제
```c
void printNumber(int n) {
  printf("%d\n", n);
}

int main() {
  void (*funcPtr)(int) = printNumber;
  funcPtr(42);  // 함수 호출
}
```

### A.5 C++ 스마트 포인터 관련 생성자 및 메서드
| 스마트 포인터 유형           | 생성 및 사용 예                                  |
| -------------------- | ------------------------------------------ |
| `std::unique_ptr<T>` | `auto p = std::make_unique<T>();`          |
| `std::shared_ptr<T>` | `auto p = std::make_shared<T>();`          |
| `std::weak_ptr<T>`   | `std::weak_ptr<T> w = p;` (shared\_ptr 기반) |

메서드	설명
| 메서드            | 설명                             |
| -------------- | ------------------------------ |
| `.get()`       | 내부 raw 포인터 반환                  |
| `.reset()`     | 소유 포인터 해제                      |
| `.use_count()` | shared\_ptr 참조 카운트 확인          |
| `.lock()`      | weak\_ptr → shared\_ptr로 변환 시도 |

### A.6 기타 포인터 관련 함수
| 함수 또는 연산자  | 설명                      |
| ---------- | ----------------------- |
| `&`        | 주소 연산자 (변수 주소 얻기)       |
| `*`        | 간접 참조 연산자 (주소에서 값 가져오기) |
| `sizeof()` | 변수 또는 자료형의 크기(바이트) 반환   |
| `memset()` | 메모리를 특정 값으로 초기화         |
| `memcpy()` | 메모리 블록 복사               |

💡 예제
```c
int arr[5];
memset(arr, 0, sizeof(arr));  // arr 전체를 0으로 초기화
```
### A.7 요약표: 주요 함수 목적별 정리
| 범주      | 주요 함수 및 용도                                           |
| ------- | ---------------------------------------------------- |
| 메모리 할당  | `malloc`, `calloc`, `realloc`, `free`                |
| 문자열 처리  | `strlen`, `strcpy`, `strcmp`, `strcat`               |
| 포인터 검사  | `NULL 체크`, `== 비교`                                   |
| 함수 포인터  | `(*fptr)(args)`                                      |
| 스마트 포인터 | `make_unique`, `make_shared`, `.get()`, `.reset()` 등 |
| 기타 유틸   | `memset`, `memcpy`, `sizeof`, `&`, `*`               |
