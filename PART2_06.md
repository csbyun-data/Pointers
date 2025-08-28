🔹 2.6 메모리 맵 탐색 및 구조체 메모리 관리
✅ 2.6.1 구조체 메모리 시각화 예제

개념 요약:

구조체는 **자료형별 정렬 규칙(alignment)** 에 따라 메모리에 배치됩니다.
이는 포인터 연산이나 메모리 최적화에서 중요한 영향을 미칩니다.

📌 구조체 예제와 시각화
```C
#include <stdio.h>
#include <stddef.h>

struct Example {
    char a;    // 1 byte
    int b;     // 4 bytes
    char c;    // 1 byte
};

int main() {
    struct Example ex;
    printf("Size of struct: %zu bytes\n", sizeof(struct Example));
    printf("Offset of a: %zu\n", offsetof(struct Example, a));
    printf("Offset of b: %zu\n", offsetof(struct Example, b));
    printf("Offset of c: %zu\n", offsetof(struct Example, c));
    return 0;
}
```

출력 예 (시스템에 따라 달라질 수 있음):
```
Size of struct: 12 bytes
Offset of a: 0
Offset of b: 4
Offset of c: 8
```

해석:
| 멤버  | 실제 크기   | 정렬된 위치 |
| --- | ------- | ------ |
| `a` | 1 byte  | 0      |
|     | padding | 1\~3   |
| `b` | 4 bytes | 4\~7   |
| `c` | 1 byte  | 8      |
|     | padding | 9\~11  |


💡 padding은 CPU 메모리 접근 최적화를 위한 여유 공간입니다.

✅ 구조체 배열에서 포인터 활용
```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Student {
    int id;
    char name[20];
};

int main() {
    int n = 3;
    struct Student* students = (struct Student*)malloc(sizeof(struct Student) * n);

    // 초기화
    for (int i = 0; i < n; i++) {
        students[i].id = i + 1;
        sprintf(students[i].name, "Student_%d", i + 1);
    }

    // 포인터를 이용한 순회
    struct Student* ptr = students;
    for (int i = 0; i < n; i++, ptr++) {
        printf("ID: %d, Name: %s\n", ptr->id, ptr->name);
    }

    free(students);
    return 0;
}
```
✅ 구조체 내부 포인터 멤버 다루기
```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Book {
    char* title;
};

int main() {
    struct Book b;
    b.title = (char*)malloc(100);
    strcpy(b.title, "C Programming in Practice");

    printf("Book Title: %s\n", b.title);

    free(b.title);
    return 0;
}
```

📌 구조체 안에 포인터가 있는 경우, 포인터가 가리키는 메모리까지 별도로 할당/해제해야 합니다.

✅ 중첩 구조체와 포인터
```C
#include <stdio.h>
#include <stdlib.h>

struct Date {
    int year, month, day;
};

struct Person {
    char name[20];
    struct Date* birth;
};

int main() {
    struct Person p;
    strcpy(p.name, "Alice");

    p.birth = (struct Date*)malloc(sizeof(struct Date));
    p.birth->year = 1990;
    p.birth->month = 7;
    p.birth->day = 15;

    printf("%s was born on %d-%02d-%02d\n", p.name, p.birth->year, p.birth->month, p.birth->day);

    free(p.birth);
    return 0;
}
```
⚠️ 메모리 관리 주의사항 (구조체 + 포인터)
| 상황                 | 조치                                                           |
| ------------------ | ------------------------------------------------------------ |
| 구조체 안에 동적 포인터 있을 때 | `free()`를 명시적으로 해줘야 함                                        |
| 구조체 배열 할당          | `sizeof(StructType) * N` 정확히 계산                              |
| 패딩 방지              | `#pragma pack(1)` or `__attribute__((packed))` 사용 가능 (주의 필요) |
| 포인터를 구조체로 캐스팅      | 반드시 바이트 정렬, 주소 정합성 확인 필요                                     |

📘 메모리 레이아웃 시각 도구 (추천)

* Godbolt Compiler Explorer

* hexdump, gdb, xxd 등으로 메모리 확인 가능

* 시뮬레이터: C 구조체 시각화 웹도구 (Visualgo 등)
