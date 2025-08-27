📘 1.5 포인터와 함수 매개변수 전달 (Call by Value & Call by Reference)
🧠 개요

C 언어에서는 함수에 인자를 전달할 때 기본적으로 Call by Value (값에 의한 전달) 방식이 사용됩니다.
하지만 포인터를 이용하면 Call by Reference (참조에 의한 전달) 효과를 얻을 수 있습니다.

🔍 1.5.1 Call by Value (값에 의한 전달)

* 변수의 복사본이 함수에 전달됨.

* 함수 내부에서 값을 바꾸더라도 원래 변수에는 영향 없음.

🧪 예제: Call by Value
```c
#include <stdio.h>

void change(int x) {
    x = 100;
}

int main() {
    int num = 10;
    change(num);
    printf("num = %d\n", num); // 출력: 10 (변화 없음)
    return 0;
}
```
🔍 1.5.2 Call by Reference (참조에 의한 전달)

* 변수의 주소를 함수에 전달 → 포인터를 통해 원래 변수의 값을 변경할 수 있음.

* C에서는 포인터를 이용해 Call by Reference처럼 동작시킴.

🧪 예제: Call by Reference (포인터 이용)
```c
#include <stdio.h>

void change(int *p) {
    *p = 100;
}

int main() {
    int num = 10;
    change(&num);
    printf("num = %d\n", num); // 출력: 100
    return 0;
}
```
🔁 비교 정리
| 구분                | 설명     | 변수 변경 여부   |
| ----------------- | ------ | ---------- |
| Call by Value     | 값만 전달  | ❌ 원본 변경 없음 |
| Call by Reference | 주소를 전달 | ✅ 원본 변경 가능 |

🧪 예제: 두 수를 교환하는 함수
❌ Call by Value 실패 예제
```c
#include <stdio.h>

void swap(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
}

int main() {
    int x = 1, y = 2;
    swap(x, y);
    printf("x = %d, y = %d\n", x, y); // 출력: x = 1, y = 2
    return 0;
}
```
✅ Call by Reference 성공 예제
```c
#include <stdio.h>

void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int main() {
    int x = 1, y = 2;
    swap(&x, &y);
    printf("x = %d, y = %d\n", x, y); // 출력: x = 2, y = 1
    return 0;
}
```
🧪 예제: 함수에서 동적 메모리 할당

함수 내부에서 메모리를 할당하고 그 결과를 외부 변수에 저장하려면 이중 포인터가 필요합니다.
```c
#include <stdio.h>
#include <stdlib.h>

void allocateArray(int **arr, int size) {
    *arr = (int *)malloc(size * sizeof(int));
    for (int i = 0; i < size; i++) {
        (*arr)[i] = i * 10;
    }
}

int main() {
    int *numbers = NULL;
    allocateArray(&numbers, 5);

    for (int i = 0; i < 5; i++) {
        printf("%d ", numbers[i]);
    }
    printf("\n");

    free(numbers);
    return 0;
}
```
❗ 실수 방지 팁
| 실수 유형                       | 주의 사항                   |
| --------------------------- | ----------------------- |
| 포인터로 주소를 넘기지 않음             | 원래 변수에 반영되지 않음          |
| `*`와 `&` 혼동                 | `*p`는 값 접근, `&x`는 주소 전달 |
| 이중 포인터 안 쓰면 malloc 결과 반영 안됨 | 꼭 `int **p`로 받기         |

✅ 연습 문제

1. int x = 5, y = 10;을 교환하는 swap 함수를 Call by Reference로 작성하세요.

2. int *p를 함수에 넘겨 20을 저장하도록 하세요.

3. 함수에서 char *를 동적 할당 받아 "Hello"를 저장하고 출력하세요.

💡 요약

* C는 기본적으로 Call by Value.

* 포인터를 사용하면 Call by Reference처럼 동작 가능.

* 변수 값을 함수 내에서 변경하려면 주소를 전달해야 함.

* 메모리 할당 및 구조체 처리에도 필수적인 개념.
