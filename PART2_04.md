🔹 2.4 문자열 처리 라이브러리 예제
📌 개요

C 언어의 문자열은 널 종료 문자 \0를 포함한 char 배열입니다.
문자열 관련 처리는 포인터를 많이 사용하며, 대표적인 표준 함수는 다음과 같습니다:

* strlen() – 문자열 길이 계산

* strcpy() – 문자열 복사

* strcmp() – 문자열 비교

* strcat() – 문자열 연결

이 파트에서는 위 함수들을 직접 포인터로 구현하면서 포인터의 실전 사용법을 이해합니다.

✅ 2.4.1 strlen() 구현
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
✅ 2.4.2 strcpy() 구현
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
✅ 2.4.3 strcmp() 구현
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
✅ 2.4.4 strcat() 구현
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
🧠 보충: 문자열은 왜 포인터로 많이 다룰까?

* 배열은 이름 자체가 주소

* 문자열 리터럴은 메모리 상에 고정된 영역을 가리킴

* 문자 하나씩 접근해야 하므로 포인터 이동 방식이 효율적

⚠️ 주의: 문자열 처리 시 자주 발생하는 실수
| 문제           | 설명                                        |
| ------------ | ----------------------------------------- |
| 버퍼 오버플로우     | `strcpy()` 할 때 대상 크기 확인 누락                |
| NULL 포인터 참조  | `strcpy(NULL, src)` 등                     |
| 읽기 전용 메모리 쓰기 | `char* s = "abc"; s[0] = 'A';` → 오류 발생 가능 |
