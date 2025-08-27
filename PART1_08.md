📘 1.8 포인터와 문자열

C 언어에서 문자열은 문자 배열(char[]) 또는 *문자 포인터(char)**를 통해 표현됩니다. 포인터와 문자열은 매우 밀접한 관계에 있으며, 문자열 조작 함수 구현이나 처리 시 포인터 사용이 필수입니다.

🔍 1.8.1 문자열 상수와 배열
✅ 설명

* 문자열 리터럴 "Hello"는 **null 문자 \0**로 끝나는 문자 배열입니다.

* char *str = "Hello"; 는 문자열 상수를 가리키는 포인터입니다.

* 문자열은 읽기 전용 메모리 영역에 저장되므로, 수정하면 오류가 발생할 수 있습니다.

🧪 예제
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
🔍 1.8.2 문자열과 포인터 관계
✅ 설명

* 문자열은 포인터를 통해 간접적으로 접근 가능.

* 포인터 연산을 통해 각 문자에 접근하거나 조작할 수 있음.

🧪 예제: 포인터를 사용한 문자열 순회
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
🔍 1.8.3 문자열 함수 구현 (strlen, strcpy, strcmp)
✅ strlen 구현
```c
int my_strlen(const char *str) {
    int len = 0;
    while (*str++) len++;
    return len;
}
```
✅ strcpy 구현
```c
void my_strcpy(char *dest, const char *src) {
    while (*src) {
        *dest++ = *src++;
    }
    *dest = '\0';
}
```
✅ strcmp 구현
```c
int my_strcmp(const char *s1, const char *s2) {
    while (*s1 && (*s1 == *s2)) {
        s1++;
        s2++;
    }
    return *(unsigned char *)s1 - *(unsigned char *)s2;
}
```
🧪 사용 예제
```c
#include <stdio.h>

int main() {
    char str1[20];
    my_strcpy(str1, "Test");
    printf("Copied: %s\n", str1);
    printf("Length: %d\n", my_strlen(str1));
    printf("Compare: %d\n", my_strcmp("abc", "abd"));
    return 0;
}
```
🔍 1.8.4 동적 메모리 할당과 문자열 조작
✅ 설명

* 문자열을 동적으로 복사하거나 조작할 경우 malloc, free를 사용.

* 메모리 누수나 Dangling Pointer에 주의해야 함.

🧪 예제: 문자열 복사 (동적 메모리)
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
📌 보충: 문자열 관련 실수들
| 실수               | 설명                                                      |
| ---------------- | ------------------------------------------------------- |
| 포인터로 선언한 문자열 수정  | `"Hello"`는 읽기 전용이므로 수정 시 에러 발생 가능                       |
| 메모리 할당 없이 문자열 복사 | `char *p; strcpy(p, "Hello");` → **Segmentation fault** |
| 메모리 해제 누락        | `malloc`으로 할당한 메모리 `free`하지 않으면 누수 발생                   |

✅ 연습 문제

1. my_strdup() 함수를 직접 구현해 보세요. (동적 메모리로 문자열 복사)

2. 문자열을 입력받아 공백을 제거하는 함수를 포인터로 구현해 보세요.

3. 문자열 배열(char *arr[])을 정렬하는 프로그램을 만들어 보세요.
