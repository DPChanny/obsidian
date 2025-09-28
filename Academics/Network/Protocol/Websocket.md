---
type: academic
---
## 개념

WebSocket은 클라이언트와 서버가 **하나의 TCP 연결을 유지한 채**, 실시간으로 데이터를 **양방향으로 주고받을 수 있도록 만든 프로토콜**이다. HTTP처럼 TCP 위에서 동작하지만, 최초 핸드셰이크 이후에는 완전히 다른 **바이너리 프레임 기반 프로토콜**로 전환된다. 주식 시세, 게임, 실시간 채팅 등 지속적이고 즉각적인 통신이 필요한 상황에서 사용된다.

---

## 핸드셰이크

### 클라이언트 요청

```
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: <랜덤 값>
Sec-WebSocket-Version: 13

```

### 서버 응답

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: <SHA1 + Base64 결과>

```

---

## Pub/Sub 구현 방식

WebSocket 자체는 단순 데이터 통로일 뿐이며, **Pub/Sub은 상위 메시지 포맷을 통해 구현**된다.

### 예시 JSON 메시지

```json
{ "type": "subscribe", "channel": "chat" }
{ "type": "publish", "channel": "chat", "message": "hello" }

```

- 서버는 채널별로 구독자를 관리하고
- 메시지를 구독자에게 브로드캐스트하는 구조로 구성

---

## 따라서

WebSocket은 텍스트나 바이너리 모두 프레임 단위로 자유롭게 주고받을 수 있고, 핑퐁 메커니즘을 통해 연결 상태를 지속적으로 확인할 수밖에 없다. 하지만 Pub/Sub 구조는 WebSocket이 제공하는 기능이 아니며, **애플리케이션에서 직접 메시지 포맷을 설계하고 해석해야만 한다.** 결국 WebSocket도 다른 프로토콜처럼 **socket 위에서 약속된 방식으로 데이터를 프레임화하여 주고받는 하나의 구현체일 뿐**이다.