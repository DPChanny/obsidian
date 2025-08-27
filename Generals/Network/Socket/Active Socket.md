---
type: academic
parent: "[[Generals/Network/Socket|Socket]]"
---
## 개념

**Active Socket**은 **클라이언트와 서버 간의 TCP 연결이 성립된 후, 실제 데이터 송수신을 담당하는 소켓**이다.

서버 측에서는 `accept()` 호출을 통해 passive socket에서 분기되어 생성된다.

클라이언트 측에서는 `connect()` 호출 이후 만들어지는 소켓 자체가 active socket이다.

즉, **서버 입장에서는 passive socket이 연결을 수락하고 나면, 그 연결을 처리할 새로운 active socket을 커널이 생성해준다.**

클라이언트 입장에서는 처음 생성된 소켓이 곧바로 active socket 역할을 한다.

---

## 특징

- 데이터를 `send()`와 `recv()`로 송수신할 수 있는 유일한 소켓
- 고유한 4-튜플 `(클라이언트 IP, 클라이언트 포트, 서버 IP, 서버 포트)`로 식별됨
- 커널은 이 정보를 바탕으로 도착한 패킷을 해당 소켓으로 라우팅함
- 한 서버가 수많은 클라이언트와 연결되면, 각각 별도의 active socket이 생성됨

---

## 흐름 요약

서버 쪽 흐름:

```
socket()  → passive
bind()
listen()  → passive socket ready
accept()  → active socket 생성
recv()/send()  → 데이터 송수신

```

클라이언트 쪽 흐름:

```
socket() → connect()  → active socket 생성
send()/recv() → 데이터 송수신

```

---

## 예제 (Python)

### 서버 측

```python
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(('localhost', 12345))
server.listen()

print("waiting...")
conn, addr = server.accept()  # 여기서 active socket 생성
print("connected:", addr)

conn.sendall(b'hello')  # active socket 사용
conn.close()
server.close()

```

### 클라이언트 측

```python
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect(('localhost', 12345))  # 이 소켓이 active socket
data = client.recv(1024)
print("received:", data)
client.close()

```

```
서버:
waiting...
connected: ('127.0.0.1', 53422)

클라이언트:
received: b'hello'

```

---

## 따라서

active socket은 연결이 성립된 후, **유일하게 데이터 송수신을 수행할 수 있는 실체다.**

TCP 통신은 결국 이 소켓을 통해서만 이뤄질 수밖에 없으며,

passive socket은 오직 이 active socket을 만들기 위한 전초 단계에 불과하다.