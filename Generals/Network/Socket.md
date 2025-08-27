---
type: academic
parent: "[[Academics/Network|Network]]"
---
## 개념

Socket은 네트워크 통신을 위한 **양방향 통신 채널**이다.

정확히는 **응용 계층이 전송 계층(TCP/UDP)을 사용할 수 있도록 만든 운영체제 수준의 인터페이스**이다.

응용 프로그램은 Socket을 호출해서 포트 연결, 데이터 전송, 응답 수신을 처리하며,

전송 계층의 TCP/UDP 프로토콜은 그 안에서 동작한다.

## 구조적 역할

- **IP 주소 + 포트번호** 조합으로 연결 지점 지정
- **TCP 연결(stream)** 또는 **UDP 메시지(datagram)** 송수신
- 항상 **바이트(byte) 단위 송수신**, 그 외 데이터는 인코딩 필요
- **운영체제가 네트워크 스택 내부에서 처리**

## Python에서의 기본 구조

```python
import socket

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.connect(("127.0.0.1", 5000))

```

- `AF_INET`: IPv4 주소 체계 사용
- `SOCK_STREAM`: TCP 스트림 방식
- `connect`: 서버와 연결

## 바이트 전송 예시

```python
data = bytes([0x48, 0x65, 0x6C, 0x6C, 0x6F])  # 'Hello'에 해당하는 바이트
sock.sendall(data)

```

송신 바이트 스트림:

```
0x48 0x65 0x6C 0x6C 0x6F

```

수신 예시:

```python
response = sock.recv(1024)

```

응답 바이트 스트림:

```
0x52 0x45 0x53 0x50 0x4F 0x4E 0x53 0x45

```

## 따라서

모든 언어와 프레임워크는 Socket과 유사한 인터페이스를 제공한다.

Java의 `Socket`, Go의 `net.Conn`, Node.js의 `net.Socket`, Rust의 `TcpStream` 등은 동일한 구조를 따른다.

**Socket 인터페이스 위에서 HTTP, gRPC, GraphQL, WebSocket 등 고수준 프로토콜이 구현되며, 이 계층이 없다면 상위 네트워크 기능도 존재할 수밖에 없다.**