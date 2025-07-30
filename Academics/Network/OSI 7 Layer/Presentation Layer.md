---
type: academic
parent: "[[Academics/Network/OSI 7 Layer|OSI 7 Layer]]"
---
## 개념

표현 계층은 **응용 계층과 전송 계층 사이에서, 데이터의 형식을 변환하거나 해석하는 역할**을 한다.

여기서 말하는 "형식"이란 인간이 이해할 수 있는 문자, 구조화된 데이터(JSON, XML), 바이너리 포맷 등을 의미한다.

## 왜 필요한가?

전송 계층은 오직 바이트만 다룬다.

그러므로 어떤 데이터든 **Socket을 통해 전송하려면 바이트로 인코딩**되어야 하고,

수신한 데이터는 **정확한 방식으로 디코딩**해야 의미 있는 정보가 된다.

즉, 실제로는 언어에서의 `encode()`, `decode()`, `json.dumps()`, `json.loads()` 같은 호출이 이 계층에 해당한다.

## 주요 개념

### 1. 문자 인코딩 (Encoding)

### UTF-8

```python
"안녕".encode("utf-8")
# b'\\\\xec\\\\x95\\\\x88\\\\xeb\\\\x85\\\\x95'

```

### ASCII

```python
"hello".encode("ascii")
# b'hello'

```

### 2. 구조화 인코딩 (Serialization)

### JSON

```python
import json
msg = {"user": "kim", "msg": "hello"}
json_bytes = json.dumps(msg).encode("utf-8")

```

### 3. 반대 과정 (Decoding / Parsing)

```python
raw = sock.recv(1024)
decoded = raw.decode("utf-8")
msg_obj = json.loads(decoded)

```

## Socket과의 관계

Socket은 오직 바이트를 송수신한다.

표현 계층에서의 인코딩/디코딩 작업을 정확히 처리하지 않으면

**깨진 문자, JSON 파싱 오류, 한글 깨짐, 암호화 실패** 등이 발생할 수밖에 없다.

```python
sock.sendall("안녕".encode("utf-8"))
# 전송: b'\\\\xec\\\\x95\\\\x88\\\\xeb\\\\x85\\\\x95'

```

```
0xEC 0x95 0x88 0xEB 0x85 0x95

```

## 따라서

표현 계층은 형식적으론 단순한 전환 작업처럼 보이지만,

**데이터 해석의 정확성을 보장하는 핵심 지점**이며, 모든 프로그래밍 언어와 프레임워크에서 동일하게 동작할 수밖에 없다.

이 계층이 없으면 전송된 바이트는 해석할 수 없는 쓰레기 값이 되고, **상위 응용 계층의 의미도 무력화된다.**