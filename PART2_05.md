## 2.5 포인터 기반 버퍼 및 네트워크 데이터 처리
* 네트워크, 파일, 시스템 프로그래밍에서는 데이터를 **연속된 바이트 스트림(버퍼)**로 다루는 경우가 많습니다.
* 이 데이터를 효율적으로 해석하려면 포인터 연산과 **형변환(Casting)**이 필수입니다.

### 2.5.1 바이너리 버퍼에서 데이터 파싱 예제
> 가정: 서버로부터 받은 8바이트짜리 데이터가 다음과 같이 구성되어 있음
> 앞 4바이트: int 타입 (숫자)
> 뒤 4바이트: float 타입 (실수)
```C
#include <stdio.h>
#include <stdint.h>
#include <string.h>

int main() {
  uint8_t buffer[8];
  
  // 예시 데이터 구성
  int val_int = 123456;
  float val_float = 3.14f;
  
  // 메모리에 값 복사
  memcpy(buffer, &val_int, sizeof(int));           // 앞 4바이트에 int 저장
  memcpy(buffer + 4, &val_float, sizeof(float));   // 다음 4바이트에 float 저장
  
  // 파싱
  int parsed_int = *(int*)(buffer);
  float parsed_float = *(float*)(buffer + 4);
  
  printf("Parsed int: %d\n", parsed_int);
  printf("Parsed float: %.2f\n", parsed_float);
  
  return 0;
}
```

* 해설:
> uint8_t 배열은 일반적인 바이너리 버퍼를 의미합니다.
> 포인터 형변환과 오프셋 이동을 통해 값을 해석합니다.
> memcpy()는 메모리 복사를 위해 사용.

### 2.5.2 문자열 패킷 파싱 (가변 길이)
> 가정: 버퍼 형식 → [1바이트 길이][n바이트 문자열]
```C
#include <stdio.h>
#include <stdint.h>
#include <string.h>

int main() {
  uint8_t buffer[100];

  // 문자열 삽입 예시
  const char* msg = "Hello!";
  uint8_t len = strlen(msg);
  buffer[0] = len;
  memcpy(buffer + 1, msg, len);

  // 파싱
  uint8_t parsed_len = buffer[0];
  char parsed_msg[50];
  memcpy(parsed_msg, buffer + 1, parsed_len);
  parsed_msg[parsed_len] = '\0'; // 널 종료

  printf("Parsed (%d bytes): %s\n", parsed_len, parsed_msg);
  return 0;
}
```

✔ 출력 예시:
```
Parsed (6 bytes): Hello!
```

### 2.5.3 포인터를 활용한 버퍼 탐색 루틴
* 다수의 문자열을 포함한 연속된 버퍼 처리 예제
```C
#include <stdio.h>
#include <stdint.h>
#include <string.h>

int main() {
  uint8_t buffer[100];
  const char* messages[] = {"Hi", "World", "ChatGPT"};
  uint8_t* ptr = buffer;

  // 패킷 만들기 (각 메시지: [길이][내용])
  for (int i = 0; i < 3; i++) {
    uint8_t len = strlen(messages[i]);
    *ptr++ = len;
    memcpy(ptr, messages[i], len);
    ptr += len;
  }

  // 버퍼 탐색
  ptr = buffer;
  while (ptr < buffer + sizeof(buffer)) {
    uint8_t len = *ptr++;
    if (len == 0 || ptr + len > buffer + sizeof(buffer)) break;

    char temp[50];
    memcpy(temp, ptr, len);
    temp[len] = '\0';

    printf("Found: %s\n", temp);
    ptr += len;
  }

  return 0;
}
```

* 실전 활용 분야

| 분야         | 활용 예              |
| ---------- | ----------------- |
| 네트워크 프로그래밍 | 패킷 파싱, 바이트 스트림 처리 |
| IoT / 임베디드 | 센서 데이터 직렬화/역직렬화   |
| 파일 처리      | 바이너리 파일 포맷 파싱     |
| 게임 개발      | 리소스 로딩, 메시지 파싱    |

⚠️ 주의사항
| 위험 요소       | 설명                                   |
| ----------- | ------------------------------------ |
| 오프셋 계산 오류   | 포인터 위치 계산이 잘못되면 메모리 침범 가능            |
| Endianness  | 시스템의 바이트 순서 차이 주의                    |
| 정렬(Padding) | 구조체를 직접 복사할 경우 예상치 못한 여유 공간이 생길 수 있음 |
| 경계 검사 누락    | buffer overflow로 이어질 수 있음            |
