---
type: academic
parent: "[[Generals/Network/Frame|Frame]]"
---
## 1. 메시지 프레임 구조

```
POST /upload HTTP/1.1\\\\r\\\\n
Host: example.com\\\\r\\\\n
Content-Type: application/json\\\\r\\\\n
Content-Length: 27\\\\r\\\\n
\\\\r\\\\n
{"name": "kim", "age": 25}

```

---

## 2. 바디 전송 방식

### Content-Length

- 고정 바디 크기 전송
- TCP가 메시지 구분을 못 하기 때문에 필수

### Transfer-Encoding: chunked

```
4\\\\r\\\\n
Wiki\\\\r\\\\n
5\\\\r\\\\n
pedia\\\\r\\\\n
0\\\\r\\\\n
\\\\r\\\\n

```

- 바디 크기를 모를 때 청크 단위로 전송

---

## 3. Content-Type

```
Content-Type: text/html; charset=utf-8

```

- 바디 형식과 문자 인코딩 정의

---

## 4. HTML/문자열 전송 예시

```python
html = "<html><body>Hello</body></html>"
body = html.encode("utf-8")

response = (
    "HTTP/1.1 200 OK\\\\r\\\\n"
    "Content-Type: text/html; charset=utf-8\\\\r\\\\n"
    f"Content-Length: {len(body)}\\\\r\\\\n"
    "\\\\r\\\\n"
).encode("utf-8") + body

sock.sendall(response)

```

---

## 5. 연결 관리

```
Connection: keep-alive  # 기본 유지
Connection: close       # 요청 후 종료

```

---

## 6. 수신 시 디코딩 함수

```python
def parse_http_response(raw_bytes: bytes):
    header_end = raw_bytes.find(b"\\\\r\\\\n\\\\r\\\\n")
    if header_end == -1:
        raise ValueError("Invalid HTTP format")

    header_raw = raw_bytes[:header_end].decode("iso-8859-1")
    body_raw = raw_bytes[header_end + 4:]

    headers = {}
    for line in header_raw.split("\\\\r\\\\n")[1:]:
        if ": " in line:
            k, v = line.split(": ", 1)
            headers[k.lower()] = v

    content_length = int(headers.get("content-length", 0))
    body = body_raw[:content_length]

    charset = "utf-8"
    content_type = headers.get("content-type", "")
    if "charset=" in content_type:
        charset = content_type.split("charset=")[-1].strip()

    try:
        decoded = body.decode(charset)
    except:
        decoded = body

    return {
        "headers": headers,
        "charset": charset,
        "body": decoded,
        "raw_body": body
    }

```

---

## 7. 핵심 요약

|요소|역할|필수 여부|
|---|---|---|
|`Content-Type`|바디 형식 지정|O|
|`Content-Length`|바디 크기 명시|O (chunked가 아닐 경우)|
|`Transfer-Encoding: chunked`|스트리밍 전송|Content-Length 대신|
|`charset=`|바디 디코딩 기준|O (text 형식일 경우)|

---

## 따라서

> HTTP/1.1은 줄처럼 보이지만, 끝을 정확히 알리는 구조 없이는 절대 해석될 수 없다.
> 
> 결국 모든 바디는 바이트이며, 그 경계와 해석은 명시적으로 지정할 수밖에 없다.