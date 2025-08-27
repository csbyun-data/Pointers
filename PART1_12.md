📘 1.12 파일 입출력과 포인터

파일 입출력은 데이터를 영구 저장하거나 외부에서 불러올 때 필요하며, 포인터는 이 과정에서 핵심 역할을 합니다. 특히 파일 포인터 (FILE *) 는 C의 표준 라이브러리 stdio.h에서 정의되어 있으며, fopen(), fread(), fwrite() 등 함수와 함께 사용됩니다.

✅ 1.12.1 파일 포인터 개념

* FILE *fp: 파일을 가리키는 포인터

* fopen(): 파일을 열고, 파일 포인터 반환

* fclose(): 파일을 닫음

📌 예제: 텍스트 파일 열기
```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("sample.txt", "r");

    if (fp == NULL) {
        printf("파일을 열 수 없습니다.\n");
        return 1;
    }

    fclose(fp);
    return 0;
}
```
✅ 1.12.2 텍스트 파일과 바이너리 파일 입출력
✏️ 텍스트 쓰기 모드: "w"
```c
FILE *fp = fopen("output.txt", "w");
fprintf(fp, "Hello, world!\n");
fclose(fp);
```
📖 텍스트 읽기 모드: "r"
```c
char buffer[100];
FILE *fp = fopen("output.txt", "r");
fgets(buffer, sizeof(buffer), fp);
printf("%s", buffer);
fclose(fp);
```
📦 바이너리 파일 쓰기
```c
FILE *fp = fopen("data.bin", "wb");
int nums[3] = {1, 2, 3};
fwrite(nums, sizeof(int), 3, fp);
fclose(fp);
```
📦 바이너리 파일 읽기
```c
FILE *fp = fopen("data.bin", "rb");
int nums[3];
fread(nums, sizeof(int), 3, fp);
fclose(fp);
```
✅ 1.12.3 구조체 배열과 파일 연동
🧪 구조체 배열을 텍스트 파일에 저장
```c
#include <stdio.h>

typedef struct {
    char name[20];
    int age;
} Person;

int main() {
    Person list[2] = {{"Kim", 25}, {"Lee", 30}};
    FILE *fp = fopen("people.txt", "w");

    for (int i = 0; i < 2; i++) {
        fprintf(fp, "%s %d\n", list[i].name, list[i].age);
    }

    fclose(fp);
    return 0;
}
```
📖 저장된 구조체 데이터를 읽어오기
```c
int main() {
    Person list[2];
    FILE *fp = fopen("people.txt", "r");

    for (int i = 0; i < 2; i++) {
        fscanf(fp, "%s %d", list[i].name, &list[i].age);
    }

    fclose(fp);

    for (int i = 0; i < 2; i++) {
        printf("%s (%d)\n", list[i].name, list[i].age);
    }

    return 0;
}
```
✅ 1.12.4 파일 입출력 오류 처리
🚨 오류 체크 포인트

* FILE *fp == NULL: 파일 열기 실패

* 파일 경로, 권한 확인

* fscanf()나 fgets()의 반환값 체크

* fflush(stdin) 사용은 비표준 ⇒ 사용 지양

✅ 1.12.5 동적 구조체 배열 + 파일 입출력
🔄 구조체 개수 모를 때 realloc() 활용
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    char name[20];
    int age;
} Person;

int main() {
    FILE *fp = fopen("people.txt", "r");
    if (!fp) return 1;

    Person *list = NULL;
    int count = 0;

    while (!feof(fp)) {
        list = realloc(list, (count + 1) * sizeof(Person));
        fscanf(fp, "%s %d", list[count].name, &list[count].age);
        count++;
    }

    fclose(fp);

    for (int i = 0; i < count; i++) {
        printf("%s (%d)\n", list[i].name, list[i].age);
    }

    free(list);
    return 0;
}
```
✅ 1.12.6 이진 파일 입출력 + 구조체 저장
```c
typedef struct {
    char name[20];
    int age;
} Person;

int main() {
    Person people[2] = {{"Alice", 28}, {"Bob", 35}};
    FILE *fp = fopen("people.bin", "wb");
    fwrite(people, sizeof(Person), 2, fp);
    fclose(fp);

    // 읽기
    Person read_people[2];
    fp = fopen("people.bin", "rb");
    fread(read_people, sizeof(Person), 2, fp);
    fclose(fp);

    for (int i = 0; i < 2; i++) {
        printf("%s (%d)\n", read_people[i].name, read_people[i].age);
    }

    return 0;
}
```
📝 요약
| 기능     | 함수                                | 포인터 관련     |
| ------ | --------------------------------- | ---------- |
| 파일 열기  | `fopen()`                         | `FILE *fp` |
| 파일 닫기  | `fclose()`                        | 포인터 해제     |
| 텍스트 쓰기 | `fprintf()`                       | 문자열/값 출력   |
| 텍스트 읽기 | `fscanf()`, `fgets()`             | 입력 버퍼 필요   |
| 이진 쓰기  | `fwrite()`                        | 구조체 배열 저장  |
| 이진 읽기  | `fread()`                         | 구조체 배열 복원  |
| 메모리 관리 | `malloc()`, `realloc()`, `free()` | 포인터 필수     |
