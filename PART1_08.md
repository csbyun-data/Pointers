## 1.8 포인터와 문자열
* C 언어에서 문자열은 문자 배열(char[]) 또는 *문자 포인터(char)**를 통해 표현됩니다. 포인터와 문자열은 매우 밀접한 관계에 있으며, 문자열 조작 함수 구현이나 처리 시 포인터 사용이 필수입니다.

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

### 1.8.3 문자열 함수 구현 (strlen, strcpy, strcmp)  
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
예제 3: 사용 예
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

### 1.8.4 동적 메모리 할당과 문자열 조작  
* 문자열을 동적으로 복사하거나 조작할 경우 malloc, free를 사용.
* 메모리 누수나 Dangling Pointer에 주의해야 함.

예제 4: 문자열 복사 (동적 메모리)
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

📚 정리 요약
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
