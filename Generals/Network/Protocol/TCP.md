---
type: academic
parent: "[[Generals/Network/Protocol|Protocol]]"
---
## 개념

TCP는 신뢰성 있는 바이트 스트림 전송을 보장하는 전송 계층 프로토콜이다.

이는 상태 머신, 소켓 큐, 슬라이딩 윈도우, 재전송 메커니즘의 총합으로 구성된다.

---

## 목적

- 순서 보장
- 손실 복구
- 흐름/혼잡 제어
- 동시 연결 관리

---

## 상태 머신

### 클라이언트

```
CLOSED → SYN_SENT → ESTABLISHED

```

### 서버

```
CLOSED → LISTEN → SYN_RECEIVED → ESTABLISHED

```

---

## Passive Socket의 큐

### Passive Queue

- SYN_RECEIVED
- ACK 미수신 상태
- backlog 제한 있음

### Active Queue

- ESTABLISHED
- accept() 대상

```
SYN → Passive Queue
ACK → Active Queue
accept() → 사용자 반환

```

---

## Active Socket의 큐

### Send Queue

- write() → 커널 버퍼 저장
- 아직 ACK 안 받은 데이터

### Receive Queue

- 커널 수신 버퍼
- recv()로 읽음

```
Application → send() → Send Queue → TCP
TCP → Receive Queue → recv() → Application

```

---

## 예시 흐름

```
[Client]                     [Server]
connect()                 listen()
→ SYN                   → Passive Queue
← SYN+ACK               ← SYN_RECEIVED
→ ACK                   → Active Queue
                        → accept()

```