---
tags: []
related:
  - "[[Devs/Languages/Python/Toolchains/CPython|CPython]]"
---

CPython 컴파일러가 생성한 바이트코드를 한 줄씩 해석하고 실행하는 인터프리터 기반의 가상 머신임.

## 주요 메커니즘

- **Stack-based VM**: 연산 수행 시 스택(Stack) 데이터 구조를 사용하여 명령어를 처리함.
- **GIL (Global Interpreter Lock)**: 한 번에 하나의 스레드만 Python 바이트코드를 실행할 수 있도록 제한함. (멀티코어 활용 제약 요인)
- **Reference Counting**: 객체의 참조 횟수를 관리하여 메모리를 해제함.

## 구성 요소

- **Interpreter Loop**: 바이트코드 명령어를 순회하며 실행하는 루프.
- **Built-in Objects**: Python의 기본 자료형(int, list 등)의 실제 구현체.
