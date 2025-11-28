## 2.1 연결 리스트 구현  

### 2.1.1 자기 참조 구조체 이해
* 자기 참조 구조체(self-referential struct) : 자기 자신을 포인터로 참조하는 구조체
```C
struct Node {
  int data;           // 데이터
  struct Node* next;  // 다음 노드를 가리키는 포인터
};
```
> 이 구조는 Node 구조체의 포인터를 멤버로 포함하므로, 연결 리스트처럼 노드를 연결하는 데 적합합니다.
> 참고: 구조체 정의 시, 직접 구조체 타입을 멤버로 넣는 건 불가능, 그래서 포인터로 선언해야 합니다.

### 2.1.2 구조체 포인터를 이용한 노드 생성 및 연결
* 자기 참조형 구조체
```c
#include <stdio.h>

struct Node {
  int data;
  struct Node *link;
};

int main() {
  struct Node n1 = {10, NULL}, n2 = {20, NULL}, n3 = {30, NULL};
  struct Node *p = NULL;

  p = &n1;
  n1.link = &n2;
  n2.link = &n3;

  printf("%p %p %p %p\n", &p, &n1, &n2, &n3);
  printf("%d\n", p->data);
  p = p->link;
  printf("%d\n", p->data);
  p = p->link;
  printf("%d\n", p->data);

 return 0;
}
```
예제 1: 단일 연결 리스트에 노드 2개를 생성하여 연결
```C
#include <stdio.h>
#include <stdlib.h>

struct Node {
  int data;
  struct Node* next;
};

int main() {
  // 노드 생성
  struct Node* head = (struct Node*)malloc(sizeof(struct Node));
  struct Node* second = (struct Node*)malloc(sizeof(struct Node));

  // 데이터 할당
  head->data = 10;
  head->next = second;

  second->data = 20;
  second->next = NULL;

  // 출력
  struct Node* current = head;
  while (current != NULL) {
    printf("%d -> ", current->data);
    current = current->next;
  }
  printf("NULL\n");

  // 메모리 해제
  free(second);
  free(head);

  return 0;
}
```

✔ 출력 예시:
```
10 -> 20 -> NULL
```

### 2.1.3 연결 리스트 삽입, 삭제 예제
* 노드 삽입 (맨 앞에 삽입)
* 노드 삭제 (특정 값 삭제)
* 삽입 함수 예제 (앞에 삽입):
```C
void insert_front(struct Node** head_ref, int new_data) {
  struct Node* new_node = (struct Node*)malloc(sizeof(struct Node));
  new_node->data = new_data;
  new_node->next = *head_ref;
  *head_ref = new_node;
}
```

삭제 함수 예제 (값을 기준으로 삭제):
```C
void delete_node(struct Node** head_ref, int key) {
  struct Node* temp = *head_ref;
  struct Node* prev = NULL;

  if (temp != NULL && temp->data == key) {
    *head_ref = temp->next;
    free(temp);
    return;
  }

  while (temp != NULL && temp->data != key) {
    prev = temp;
    temp = temp->next;
  }

  if (temp == NULL) return;

  prev->next = temp->next;
  free(temp);
}
```
