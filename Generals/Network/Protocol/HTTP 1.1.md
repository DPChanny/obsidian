---
type: academic
parent: "[[Generals/Network/Protocol|Protocol]]"
---
## 개념

HTTP/1.1은 TCP 위에서 동작하는 텍스트 기반의 애플리케이션 계층 프로토콜이다. 클라이언트가 요청(request)을 보내고, 서버가 응답(response)을 주는 구조다. 중요한 점은 모든 메시지가 사람이 읽을 수 있는 **문자열(ASCII 텍스트)**로 구성되어 있다는 것이다.

---

## "텍스트 기반"이란?

HTTP/1.1은 메시지를 전부 **문자열 형태로 구성한다.** 요청 라인, 헤더, 본문 모두 ASCII 또는 UTF-8처럼 사람이 읽을 수 있는 형태로 전달된다.

```
GET / HTTP/1.1
Host: example.com
User-Agent: curl/7.64.1
Accept: */*

```

이게 가능한 이유는 모든 문자열이 결국 바이트로 인코딩되기 때문이다. 하지만 문자열을 바이트로 인코딩하는 방식은 여러 가지가 존재하고, 서로 다르면 전혀 다른 의미가 될 수 있다. 따라서 **어떤 인코딩을 사용했는지를 반드시 명시해야만 정확하게 해석할 수 있다.**

---

## 왜 인코딩을 명시해야 하는가?

예를 들어 `"안녕하세요"`는 UTF-8, EUC-KR, UTF-16 등 다양한 방식으로 바이트로 바뀔 수 있고, 이 중 어떤 걸 썼는지 모르면 **서버는 잘못된 문자열로 해석하거나 글자가 깨질 수밖에 없다.**

```python
"안녕하세요".encode("utf-8")
# b'\\\\xec\\\\x95\\\\x88\\\\xeb\\\\x85\\\\x95\\\\xed\\\\x95\\\\x98\\\\xec\\\\x84\\\\xb8\\\\xec\\\\x9a\\\\x94'

"안녕하세요".encode("euc-kr")
# b'\\\\xbe\\\\xc8\\\\xb3\\\\xe7\\\\xc7\\\\xcf\\\\xbc\\\\xbc\\\\xbf\\\\xe4'

```

이처럼 같은 문자열도 인코딩에 따라 바이트가 완전히 다르다. 따라서 HTTP 같은 프로토콜에서 **본문을 정확하게 처리하려면 인코딩 정보를 같이 보내야만 한다.**

---

## HTTP/1.1의 인코딩 명시 방식

HTTP/1.1은 이 문제를 해결하기 위해 **Content-Type 헤더에 인코딩(charset)을 명시하는 구조**를 채택했다.

### 요청(Request)에서:

```
Content-Type: application/json; charset=utf-8

```

### 응답(Response)에서:

```
Content-Type: text/html; charset=utf-8

```

### HTML 본문 내부에서도 명시 가능:

```html
<meta charset="utf-8">

```

---

## Keep-Alive와 단일 연결

HTTP/1.0에서는 요청마다 TCP 연결을 새로 열었다. HTTP/1.1은 `Connection: keep-alive` 헤더를 통해 **하나의 TCP 연결로 여러 요청을 처리할 수 있게 했다.** 하지만 여전히 병렬 요청은 불가능하다. 요청은 순차적으로 보내고, 응답도 순차적으로 받아야 한다. 따라서 **멀티플렉싱은 불가능**하며, 이는 성능 병목의 원인이 된다.

---

## 예시 (UTF-8 인코딩 명시)

```python
import socket

request = (
    "POST /data HTTP/1.1\\\\r\\\\n"
    "Host: example.com\\\\r\\\\n"
    "Content-Type: application/json; charset=utf-8\\\\r\\\\n"
    "Content-Length: 27\\\\r\\\\n"
    "Connection: close\\\\r\\\\n"
    "\\\\r\\\\n"
    '{"msg": "안녕하세요"}'
)

sock = socket.create_connection(("example.com", 80))
sock.send(request.encode("utf-8"))
response = sock.recv(4096)
print(response.decode("utf-8"))

```

---

## 따라서

HTTP/1.1은 사람이 읽기 쉬운 **텍스트 기반 프로토콜**이지만, 결국 모든 데이터는 바이트로 전송된다. 텍스트를 정확하게 전달하려면 **문자 인코딩을 명확히 지정해야 하며**, 이를 위해 `Content-Type` 헤더에 `charset` 속성을 사용하는 방식이 정착되었다. 인코딩을 생략하면 해석이 엇갈리고, 통신이 무의미해질 수밖에 없다. 이런 구조적 한계와 텍스트 기반의 제약이 있었기 때문에, 이후 등장한 HTTP/2는 아예 바이너리 프레임 방식으로 진화할 수밖에 없었다.