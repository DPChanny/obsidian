---
type: academic
parent: "[[Generals/Network/Frame|Frame]]"
---
## 1. 공통 프레임 헤더

|필드|크기|설명|
|---|---|---|
|Length|24비트|페이로드 길이|
|Type|8비트|프레임 종류 (0x0 ~ 0x9)|
|Flags|8비트|플래그|
|Reserved|1비트|항상 0|
|Stream ID|31비트|스트림 식별자|

---

## 2. 프레임 종류별 설명

### 0x0 — DATA

- 본문 데이터
- 플래그: `END_STREAM`, `PADDED`

### 0x1 — HEADERS

- 헤더 블록
- 플래그: `END_HEADERS`, `END_STREAM`, `PADDED`, `PRIORITY`

### 0x2 — PRIORITY

- 스트림 우선순위 설정

### 0x3 — RST_STREAM

- 스트림 종료, 에러코드 포함

### 0x4 — SETTINGS

- 연결 설정 교환
- 플래그: `ACK`

### 0x5 — PUSH_PROMISE

- 서버 푸시 예고

### 0x6 — PING

- 연결 확인
- 플래그: `ACK`

### 0x7 — GOAWAY

- 연결 종료

### 0x8 — WINDOW_UPDATE

- 수신 윈도우 증가

### 0x9 — CONTINUATION

- 헤더 블록 분할

---

## 3. DATA 프레임 구현 예시 (Python)

```python
def create_http2_data_frame(stream_id: int, data: bytes, end_stream=False) -> bytes:
    length = len(data)
    frame_type = 0x0  # DATA
    flags = 0x1 if end_stream else 0x0
    stream_id &= 0x7FFFFFFF  # 상위 1비트 제거

    length_bytes = length.to_bytes(3, 'big')
    type_byte = frame_type.to_bytes(1, 'big')
    flags_byte = flags.to_bytes(1, 'big')
    stream_id_bytes = stream_id.to_bytes(4, 'big')

    header = length_bytes + type_byte + flags_byte + stream_id_bytes
    return header + data

```

```python
# 사용 예시
frame = create_http2_data_frame(stream_id=1, data=b"Hello, HTTP/2!", end_stream=True)
print(frame)

```

---

## 따라서

- HTTP/2는 바이트 구조 기반의 프로토콜이다
- 프레임 헤더와 각 플래그, 필드의 역할을 명확히 이해해야 구현 가능하다
- DATA 프레임 하나만 해도 정확한 바이트 포맷이 없으면 수신 불가