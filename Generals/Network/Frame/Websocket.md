---
type: academic
parent: "[[Generals/Network/Frame|Frame]]"
---
## 1. 프레임 구조 개요 (바이트 단위)

```
[0]  FIN | RSV1 | RSV2 | RSV3 | OPCODE
[1]  MASK | PAYLOAD LEN (7 bits)
[2~?] Extended Payload Length (if 126 or 127)
[?]  Masking Key (4 bytes if MASK = 1)
[?]  Payload Data (masked if MASK = 1)

```

---

## 2. 각 필드 상세

### ▍1바이트: FIN + RSVx + OPCODE

- **FIN (1bit)**: 메시지의 마지막 프레임인지 (1이면 끝)
- **RSV1~3 (3bit)**: 확장용 (항상 0)
- **Opcode (4bit)**:
    - `0x0`: Continuation
    - `0x1`: Text (UTF-8)
    - `0x2`: Binary
    - `0x8`: Close
    - `0x9`: Ping
    - `0xA`: Pong

→ Python에선 `first_byte = 0x80 | opcode`로 작성 (0x80 = FIN=1)

---

### ▍1바이트: MASK + Payload Length

- **MASK (1bit)**: 클라이언트 → 서버면 항상 1
- **Length (7bit)**:
    - 0~125: 그대로 사용
    - 126: 다음 2바이트에 길이 저장
    - 127: 다음 8바이트에 길이 저장

---

### ▍확장 길이 필드 (조건부)

- Length = 126 → `2바이트` (`!H` struct 사용)
- Length = 127 → `8바이트` (`!Q` struct 사용)

---

### ▍마스크 키 + 마스킹된 데이터

- 4바이트 난수 마스크 키 사용 (클라이언트 → 서버일 때 필수)
- 마스킹: 각 바이트를 마스크 키와 XOR

```python
masked[i] = data[i] ^ masking_key[i % 4]

```

---

## 3. 마스킹이 필요한 이유

- WebSocket은 HTTP 업그레이드 방식으로 시작되므로, 프록시/중계 서버가 내용을 HTTP로 오해할 수 있음
- 마스킹은 실제 데이터 구조를 숨겨 **중간 공격/조작을 방지**
- 클라이언트만 마스킹해야 하며, 서버는 마스킹 안 함

---

## 4. `struct` 설명

- **`struct`는 바이트 포맷을 만드는 표준 라이브러리**
- 예) `struct.pack('!H', 300)` → 2바이트 big-endian 표현
- `'!'`는 네트워크 바이트 순서 (big-endian)
- `'B'`: unsigned byte (1바이트)
- `'H'`: unsigned short (2바이트)
- `'Q'`: unsigned long long (8바이트)

---

## 5. 구현 코드

```python
import os
import struct

def create_websocket_frame(payload: bytes, opcode=0x1) -> bytes:
    # 첫 바이트: FIN=1 + Opcode
    first_byte = 0x80 | (opcode & 0x0F)

    length = len(payload)
    extended = b""

    # 두 번째 바이트: MASK=1 + 길이
    if length <= 125:
        second_byte = 0x80 | length
    elif length <= 65535:
        second_byte = 0x80 | 126
        extended = struct.pack('!H', length)  # 2바이트
    else:
        second_byte = 0x80 | 127
        extended = struct.pack('!Q', length)  # 8바이트

    # 마스킹 키 (클라이언트 → 서버에서 필수)
    masking_key = os.urandom(4)

    # XOR 마스킹 수행
    masked_payload = bytes(
        payload[i] ^ masking_key[i % 4] for i in range(length)
    )

    # 프레임 조합: header + 확장길이 + 키 + 마스킹된 데이터
    return (
        struct.pack('!BB', first_byte, second_byte) +  # 1바이트 2개
        extended +                                     # 선택적 확장 길이
        masking_key +                                  # 4바이트 마스크 키
        masked_payload                                 # XOR된 본문
    )

# 예시
frame = create_websocket_frame(b"hello websocket")
print(frame)

```

---

## 6. 따라서

- WebSocket은 단순하지만 엄격한 **바이트 구조**를 가진다
- 마스킹은 보안보단 **전송 안전성과 식별 보호를 위한 XOR 조치**
- `struct`는 이 바이트 포맷을 정확하게 생성하기 위한 **필수 도구**
- WebSocket은 결국 `[헤더][확장길이?][마스크 키][마스킹 데이터]` 구조를 따를 수밖에 없다

```
Frame = [FIN + OPCODE][MASK + LEN][ExtendedLen][MaskKey][MaskedPayload]

```