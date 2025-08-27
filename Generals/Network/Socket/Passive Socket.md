---
type: academic
parent: "[[Generals/Network/Socket|Socket]]"
---
## 개념

**Passive Socket**은 TCP 서버에서 **클라이언트의 연결 요청을 수동적으로 대기하는 소켓**이다.

이 소켓은 `listen` 상태에 들어간 후, 클라이언트가 접속을 시도할 때까지 아무 작업도 하지 않는다.

단 하나의 목적, **연결을 수락하기 위한 대기 지점**으로서 존재한다.

그리고 중요한 점은, **이 passive socket은 오직 TCP의 3-way handshake 과정에서만 사용된다.**

즉, 클라이언트가 `SYN` 패킷을 보내고, 서버가 `SYN-ACK`를 응답한 뒤, 클라이언트가 `ACK`로 응답하면서 연결이 성립된다.

이 일련의 과정이 passive socket 위에서 이뤄진다.

연결이 성립되고 나면 커널은 새로운 active socket을 생성하고, 이후 모든 통신은 이 active socket으로 넘어간다.

---

## 흐름

1. **소켓 생성**
    
    먼저 일반적인 TCP 소켓을 생성한다.
    
2. **주소 바인딩**
    
    IP 주소와 포트를 소켓에 바인딩한다.
    
3. **Passive로 전환**
    
    `listen`을 호출하여 소켓을 passive 상태로 만든다.
    
4. **3-way handshake 처리**
    
    클라이언트 요청을 커널이 수락하고 `accept()`를 통해 active socket으로 전환한다.
    
    이 handshake 과정에서만 passive socket이 동작한다.
    

---

## 따라서

서버 구조에서 passive socket은 생략할 수 없는 절차다.

**listen 없이 accept는 불가능하고, passive socket 없이 연결 자체가 이뤄지지 않는다.**

**하지만 연결이 성립되고 나면 passive socket은 더 이상 사용되지 않는다.**

결국 TCP 서버를 구현한다면 passive socket을 생성할 수밖에 없다.

이는 언어나 플랫폼과 무관하게 통용되는 구조적 원칙이다.

---

## 예시 (Python)

```python
import socket

# 소켓 생성
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 주소 바인딩
server_socket.bind(('localhost', 12345))

# passive socket으로 전환
server_socket.listen(5)

print("Waiting for connection...")

# 연결 수락 → active socket 획득
client_socket, client_address = server_socket.accept()
print("Connected by", client_address)

client_socket.sendall(b"Hello")
client_socket.close()
server_socket.close()

```

```
Waiting for connection...
Connected by ('127.0.0.1', 52341)

```

---

## 요약

- passive socket은 `listen()` 호출로 생성된다.
- **TCP 3-way handshake 동안만 사용된다.**
- handshake 이후에는 active socket이 모든 통신을 담당한다.
- passive socket은 연결 요청을 수락하기 위한 구조적 관문이다.