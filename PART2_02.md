🔹 2.2 스택과 큐 구현
📌 스택(Stack) – 후입선출(LIFO)

개념 요약:

나중에 들어온 데이터가 먼저 나가는 구조.

기본 연산: push (삽입), pop (삭제), peek (맨 위 확인)

✅ 연결 리스트 기반 스택 구현
#include <stdio.h>
#include <stdlib.h>

struct StackNode {
    int data;
    struct StackNode* next;
};

// 새로운 노드 생성 및 top에 push
void push(struct StackNode** top_ref, int value) {
    struct StackNode* new_node = (struct StackNode*)malloc(sizeof(struct StackNode));
    new_node->data = value;
    new_node->next = *top_ref;
    *top_ref = new_node;
}

// top 노드 pop
int pop(struct StackNode** top_ref) {
    if (*top_ref == NULL) {
        printf("스택이 비어있습니다.\n");
        return -1;
    }
    struct StackNode* temp = *top_ref;
    int popped = temp->data;
    *top_ref = temp->next;
    free(temp);
    return popped;
}

// top 확인
int peek(struct StackNode* top) {
    if (top == NULL) return -1;
    return top->data;
}

// 출력
void print_stack(struct StackNode* top) {
    printf("Stack top -> ");
    while (top != NULL) {
        printf("%d -> ", top->data);
        top = top->next;
    }
    printf("NULL\n");
}


사용 예제:

int main() {
    struct StackNode* stack = NULL;

    push(&stack, 10);
    push(&stack, 20);
    push(&stack, 30);

    print_stack(stack); // Stack top -> 30 -> 20 -> 10 -> NULL

    printf("Top: %d\n", peek(stack)); // 30
    printf("Popped: %d\n", pop(&stack)); // 30
    print_stack(stack); // 20 -> 10 -> NULL

    return 0;
}

📌 큐(Queue) – 선입선출(FIFO)

개념 요약:

먼저 들어온 데이터가 먼저 나가는 구조.

기본 연산: enqueue (삽입), dequeue (삭제), front, rear 확인

✅ 연결 리스트 기반 큐 구현
#include <stdio.h>
#include <stdlib.h>

struct QNode {
    int data;
    struct QNode* next;
};

struct Queue {
    struct QNode* front;
    struct QNode* rear;
};

// 큐 초기화
void init_queue(struct Queue* q) {
    q->front = q->rear = NULL;
}

// 데이터 삽입
void enqueue(struct Queue* q, int value) {
    struct QNode* new_node = (struct QNode*)malloc(sizeof(struct QNode));
    new_node->data = value;
    new_node->next = NULL;

    if (q->rear == NULL) {
        q->front = q->rear = new_node;
        return;
    }

    q->rear->next = new_node;
    q->rear = new_node;
}

// 데이터 삭제
int dequeue(struct Queue* q) {
    if (q->front == NULL) {
        printf("큐가 비어 있습니다.\n");
        return -1;
    }

    struct QNode* temp = q->front;
    int value = temp->data;
    q->front = q->front->next;

    if (q->front == NULL) q->rear = NULL;
    free(temp);
    return value;
}

// 출력
void print_queue(struct Queue* q) {
    struct QNode* current = q->front;
    printf("Front -> ");
    while (current != NULL) {
        printf("%d -> ", current->data);
        current = current->next;
    }
    printf("NULL\n");
}


사용 예제:

int main() {
    struct Queue q;
    init_queue(&q);

    enqueue(&q, 10);
    enqueue(&q, 20);
    enqueue(&q, 30);

    print_queue(&q); // Front -> 10 -> 20 -> 30 -> NULL

    printf("Dequeued: %d\n", dequeue(&q)); // 10
    print_queue(&q); // 20 -> 30 -> NULL

    return 0;
}

💡 요약 정리
자료구조	연산	특징
스택	push, pop, peek	LIFO (Last-In First-Out)
큐	enqueue, dequeue, front	FIFO (First-In First-Out)
