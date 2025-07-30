---
type: academic
parent: "[[Academics/Network/OSI 7 Layer|OSI 7 Layer]]"
---
## 개념

세션 계층은 **통신하는 양쪽 사이의 연결된 상태, 즉 '대화의 연속성'을 유지하고 추적하는 계층**이다.

단순히 데이터를 주고받는 것 이상의 의미로,

> "우리는 지금 같은 대화를 이어가고 있는가?"를 관리하는 역할이다.

## 반드시 연결되어 있어야 하는가?

**아니다.**

세션은 TCP처럼 연결이 유지된 상태일 수도 있지만,

**JWT나 쿠키처럼 연결이 끊겼다가 다시 이어져도 상태가 동기화되면 세션으로 인정**할 수밖에 없다.

> 세션은 ‘연결’을 의미하는 게 아니라,
> 
> **‘상태의 일관성을 유지하는 구조’라는 점에서 존재**한다.

## WebSocket: 연결 기반 세션 구현 (Python)

```python
import websocket

def on_ping(ws, message):
    print("Received ping")

def on_pong(ws, message):
    print("Received pong")

ws = websocket.WebSocketApp(
    "wss://echo.websocket.events",
    on_ping=on_ping,
    on_pong=on_pong
)

ws.run_forever(ping_interval=60, ping_timeout=10)

```

- `ping_interval`: 60초마다 ping 전송
- `ping_timeout`: 10초 안에 pong 못 받으면 연결 끊음
- 서버와 클라이언트가 **서로의 존재를 감지하며 세션을 유지**하는 구조

## JWT: 상태 기반 세션 구현 (Python)

```python
import jwt
from datetime import datetime, timedelta

payload = {
    "user_id": "abc123",
    "exp": datetime.utcnow() + timedelta(hours=1)
}

token = jwt.encode(payload, "secret", algorithm="HS256")

```

요청 헤더에 JWT를 포함시켜 전송:

```
Authorization: Bearer <JWT토큰>

```

서버는 매 요청마다 이 토큰을 해석해 사용자 세션을 복원한다.

## 정리: 구조적 비교

|방식|연결 필요 여부|상태 유지 방식|끊겼을 때|
|---|---|---|---|
|WebSocket|O (상시연결)|TCP + ping/pong|세션 종료|
|JWT|X (요청마다 연결)|매 요청마다 상태 복원|세션 유지|

## 따라서

- 세션 계층은 응용 계층이 대화를 안정적으로 이어가기 위한 기반이 된다.
- WebSocket처럼 **물리적 연결을 유지하는 방식도 세션**이고, JWT처럼 **논리적으로 상태를 동기화하는 방식도 세션**이다.
- Python에서는 `websocket-client`나 `websockets`로 ping/pong을,`jwt`로 상태 기반 인증을 구현할 수 있다.

결과적으로, 세션 계층은

> 연결과 상태의 연속성을 추적하며,
> 
> **응용 계층이 언제나 ‘같은 대화의 흐름’을 이어갈 수 있도록 할 수밖에 없다.**