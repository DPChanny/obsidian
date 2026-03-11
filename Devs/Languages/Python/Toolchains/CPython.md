---
tags: []
related:
  - "[[Devs/Languages/Python/Runtimes/Python VM|Python VM]]"
---

Python 언어의 공식 레퍼런스 구현체로, C 언어로 작성되었으며 소스 코드를 바이트코드로 컴파일하는 과정을 담당함.

## 컴파일 과정

1. **Lexing/Parsing**: 소스 코드를 추상 구문 트리(AST)로 변환함.
2. **Bytecode Generation**: AST를 Python 가상 머신이 이해할 수 있는 `.pyc` 형태의 바이트코드를 변환함.

## 특징

- **Reference Implementation**: 새로운 Python 문법이 가장 먼저 구현되는 표준임.
- **C Extensions**: C/C++로 작성된 외부 라이브러리와의 높은 호환성을 제공함.
