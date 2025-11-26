## 1.8 포인터와 문자열
* C 언어에서 문자열은 문자 배열(char[]) 또는 문자 포인터(char *)를 통해 표현됩니다.
* 포인터와 문자열은 매우 밀접한 관계에 있으며, 문자열 조작 함수 구현이나 처리 시 포인터 사용이 필수입니다.

### 1.8.1 문자열 상수와 배열  
* 문자열 리터럴 "Hello"는 **null 문자 \0**로 끝나는 문자 배열입니다.
* char *str = "Hello"; 는 문자열 상수를 가리키는 포인터입니다.
* 문자열은 읽기 전용 메모리 영역에 저장되므로, 수정하면 오류가 발생할 수 있습니다.

예제 1:
```c
#include <stdio.h>

int main() {
  char str1[] = "Hello";       // 수정 가능
  char *str2 = "World";        // 수정 불가 (읽기 전용)

  str1[0] = 'h';               // OK
  // str2[0] = 'w';            // 오류: 런타임 에러 발생 가능

  printf("%s %s\n", str1, str2);
  return 0;
}
```
### 1.8.2 문자열과 포인터 관계  
* 문자열은 포인터를 통해 간접적으로 접근 가능.
* 포인터 연산을 통해 각 문자에 접근하거나 조작할 수 있음.

예제 2: 포인터를 사용한 문자열 순회
```c
#include <stdio.h>

int main() {
  char *str = "Pointer";

  while (*str != '\0') {
    printf("%c ", *str);
    str++;
  }
  return 0;
}
```
### 1.8.3 동적 메모리 할당과 문자열 조작  
* 문자열을 동적으로 복사하거나 조작할 경우 malloc, free를 사용.
* 메모리 누수나 Dangling Pointer에 주의해야 함.

예제 3: 문자열 복사 (동적 메모리)
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main() {
  const char *src = "Dynamic String";
  char *dest = (char *)malloc(strlen(src) + 1); // +1 for '\0'

  if (dest) {
    strcpy(dest, src);
    printf("Copied: %s\n", dest);
    free(dest);
  }

  return 0;
}
```

* 보충: 문자열 관련 실수들
  
| 실수               | 설명                                                      |
| ---------------- | ------------------------------------------------------- |
| 포인터로 선언한 문자열 수정  | `"Hello"`는 읽기 전용이므로 수정 시 에러 발생 가능                       |
| 메모리 할당 없이 문자열 복사 | `char *p; strcpy(p, "Hello");` → **Segmentation fault** |
| 메모리 해제 누락        | `malloc`으로 할당한 메모리 `free`하지 않으면 누수 발생                   |

### 1.8.4 문자열 처리 라이브러리 예제
* C 언어의 문자열은 널 종료 문자 \0를 포함한 char 배열입니다.
* 문자열 관련 처리는 포인터를 많이 사용하며, 대표적인 표준 함수는 다음과 같습니다:

> strlen() – 문자열 길이 계산  
> strcpy() – 문자열 복사  
> strcmp() – 문자열 비교  
> strcat() – 문자열 연결  

이 파트에서는 위 함수들을 직접 포인터로 구현하면서 포인터의 실전 사용법을 이해합니다.

* strlen() 구현
```C
#include <stdio.h>

size_t my_strlen(const char* str) {
  const char* ptr = str;
  while (*ptr) {
    ptr++;
  }
  return ptr - str;
}

int main() {
  char* msg = "Hello, World!";
  printf("Length: %zu\n", my_strlen(msg));  // 출력: 13
  return 0;
}
```

* strcpy() 구현
```C
#include <stdio.h>

char* my_strcpy(char* dest, const char* src) {
  char* ptr = dest;
  while (*src) {
    *ptr++ = *src++;
  }
  *ptr = '\0'; // 널 문자 포함
  return dest;
}

int main() {
  char src[] = "Pointer";
  char dest[20];

  my_strcpy(dest, src);
  printf("Copied: %s\n", dest);  // 출력: Pointer
  return 0;
}
```

* strcmp() 구현
```C
#include <stdio.h>

int my_strcmp(const char* s1, const char* s2) {
  while (*s1 && (*s1 == *s2)) {
    s1++;
    s2++;
  }
  return *(unsigned char*)s1 - *(unsigned char*)s2;
}

int main() {
  printf("%d\n", my_strcmp("apple", "apple"));  // 0
  printf("%d\n", my_strcmp("apple", "banana")); // 음수
  printf("%d\n", my_strcmp("pear", "peach"));   // 양수
  return 0;
}
```
* strcat() 구현
```C
#include <stdio.h>

char* my_strcat(char* dest, const char* src) {
  char* ptr = dest;

  // dest 끝 찾기
  while (*ptr) ptr++;

  // src 복사
  while (*src) {
    *ptr++ = *src++;
  }
  *ptr = '\0';
  return dest;
}

int main() {
  char dest[50] = "Hello";
  char* src = " World";

  my_strcat(dest, src);
  printf("Concatenated: %s\n", dest);  // 출력: Hello World
  return 0;
}
```
* 보충: 문자열은 왜 포인터로 많이 다룰까?
> 배열은 이름 자체가 주소
> 문자열 리터럴은 메모리 상에 고정된 영역을 가리킴
> 문자 하나씩 접근해야 하므로 포인터 이동 방식이 효율적

⚠️ 주의: 문자열 처리 시 자주 발생하는 실수
| 문제           | 설명                                        |
| ------------ | ----------------------------------------- |
| 버퍼 오버플로우     | `strcpy()` 할 때 대상 크기 확인 누락                |
| NULL 포인터 참조  | `strcpy(NULL, src)` 등                     |
| 읽기 전용 메모리 쓰기 | `char* s = "abc"; s[0] = 'A';` → 오류 발생 가능 |

### 1.8.5 안전한 문자열 처리
> strncpy()  – 안전한 문자열 복사    
> snprintf() – 안전한 문자열 출력  
> strncat()  – 안전한 문자열 연결

* strncpy() 구현
> NULL 종료문자(`\0`)를 직접 처리해야 합니다.
```c
#include <stdio.h>
#include <string.h>

int main() {
  char src[] = "Safe Copy Example";
  char dest[10];

  // strncpy는 최대 n개 복사하지만 널 종료는 보장하지 않음
  strncpy(dest, src, sizeof(dest) - 1);
  dest[sizeof(dest) - 1] = '\0';  // 반드시 종료 문자 추가

  printf("Copied safely: %s\n", dest);
  return 0;
}
```
* snprintf() 구현
> snprintf()는 버퍼 크기 지정, printf(), sprintf()는 길이 제한이 없어 위험합니다.  
> NULL 종료문자(`\0`) 처리를 해줌
```c
#include <stdio.h>

int main() {
  char buffer[20];
  int value = 12345;

  snprintf(buffer, sizeof(buffer), "Value: %d", value);
  printf("Formatted safely: %s\n", buffer);

  return 0;
}
```
* strncat() 구현
> strncat()은 지정한 길이까지만 문자를 연결  
> strcat()는 대상 버퍼크기 확인을 안 합니다.
> 남은 공간 계산: sizeof(dest) - strlen(dest) - 1
```c

#include <stdio.h>
#include <string.h>

int main() {
  char dest[20] = "Hello";
  char src[] = " World";

  strncat(dest, src, sizeof(dest) - strlen(dest) - 1);
  printf("Concatenated safely: %s\n", dest);

  return 0;
}
```
### 1.8.6 사용자 입력 문자열 토큰화 예제
> fgets() 문자열 입력  
> strtok() 공백 기준 토큰 분리, 첫 호출 시 문자열, 이후 호출 시 NULL 전달  
> 공백 외에도 strtok(input, " ,.;")처럼 다양한 구분자 가능  
> 입력 문자열 끝의 개행문자 제거, strlen()으로 확인 후 처리

예제 4: 사용자 입력 문자열 토큰화
```c
#include <stdio.h>
#include <string.h>

int main() {
  char input[100];

  printf("문자열을 입력하세요: ");
  if (fgets(input, sizeof(input), stdin) != NULL) {
    // 개행 문자 제거
    size_t len = strlen(input);
    if (len > 0 && input[len - 1] == '\n') {
        input[len - 1] = '\0';
    }

    printf("입력된 문자열: '%s'\n", input);

    // 토큰화 (공백 기준)
    char *token = strtok(input, " ");
    printf("토큰화 결과:\n");
    while (token != NULL) {
        printf("'%s'\n", token);
        token = strtok(NULL, " ");
    }
  } else {
    printf("입력 오류 발생!\n");
  }

  return 0;
}
```

예제 5: CSV 파싱: strtok()로 , 기준 분리
```c

#include <stdio.h>
#include <string.h>

int main() {
  char input[100];
  printf("CSV 입력 (예: apple,banana,grape): ");
  if (fgets(input, sizeof(input), stdin)) {
    size_t len = strlen(input);
    if (len > 0 && input[len - 1] == '\n') input[len - 1] = '\0';

    char *token = strtok(input, ",");
    while (token) {
      printf("과일: %s\n", token);
      token = strtok(NULL, ",");
    }
  }
  return 0;
}
```
* 동적 버퍼: 입력이 매우 길 경우 getline() 또는 malloc + realloc 사용
* 토큰 개수 카운트: 배열에 저장 후 정렬 가능
* CSV 파싱 심화 (파일에서 읽기)

* 정리 요약
  
| 문제          | 핵심 개념            | 사용 포인터                  | 주의사항             |
| ----------- | ---------------- | ----------------------- | ---------------- |
| `my_strdup` | 동적 메모리, 문자열 복사   | `malloc`, `char *`      | `\0` 포함, 해제 필요   |
| 공백 제거       | 문자열 조작, 인플레이스 편집 | 두 개의 `char *` 포인터       | 종료 문자 덮기 주의      |
| 문자열 정렬      | 포인터 배열, 버블 정렬    | `char *arr[]`, `swap()` | 문자열 비교는 `strcmp` |

✅ 연습 문제
1. my_strdup() 함수를 직접 구현해 보세요. (동적 메모리로 문자열 복사)
```c
#include <stdio.h>
#include <stdlib.h>

char *my_strdup(const char *src) {
  int len = 0;
  while (src[len]) len++;  // 문자열 길이 측정

  char *copy = (char *)malloc(len + 1);  // +1 for '\0'
  if (!copy) return NULL;

  for (int i = 0; i <= len; i++) {
    copy[i] = src[i];  // '\0' 포함 복사
  }

  return copy;
}

int main() {
  const char *original = "Hello, world!";
  char *copied = my_strdup(original);

  if (copied) {
    printf("복사된 문자열: %s\n", copied);
    free(copied);
  } else {
    printf("메모리 할당 실패\n");
  }

  return 0;
}
```
> malloc()으로 메모리 할당
> \0까지 복사 (길이 + 1)
> free()로 메모리 해제 필요

2. 공백 제거 함수 (포인터 사용)
```c
#include <stdio.h>

void remove_spaces(char *str) {
  char *read = str;
  char *write = str;

  while (*read) {
    if (*read != ' ') {
      *write = *read;
      write++;
    }
    read++;
  }

  *write = '\0';  // 종료 문자 추가
}

int main() {
  char str[] = "  T h i s   i s   a   t e s t ";
  remove_spaces(str);
  printf("공백 제거 후: '%s'\n", str);
  return 0;
}
```
> 같은 문자열 공간 내에서 read, write 두 포인터 사용
> 불필요한 공백은 건너뛰고 문자는 덮어쓰기
> 효율적이며 추가 메모리 사용 X

3. 문자열 배열 정렬 (char *arr[] 정렬)
```c
#include <stdio.h>
#include <string.h>

void swap(char **a, char **b) {
  char *temp = *a;
  *a = *b;
  *b = temp;
}

void sort_strings(char *arr[], int n) {
  for (int i = 0; i < n - 1; i++) {
    for (int j = 0; j < n - i - 1; j++) {
      if (strcmp(arr[j], arr[j + 1]) > 0) {
        swap(&arr[j], &arr[j + 1]);
      }
    }
  }
}

int main() {
  char *words[] = {"banana", "apple", "grape", "orange", "cherry"};
  int size = sizeof(words) / sizeof(words[0]);

  sort_strings(words, size);

  printf("정렬 결과:\n");
  for (int i = 0; i < size; i++) {
    printf("%s\n", words[i]);
  }

  return 0;
}
```
> char *arr[]는 포인터 배열
> 문자열 비교는 strcmp()
> 포인터 자체를 스왑하여 정렬 (복사보다 효율적)
