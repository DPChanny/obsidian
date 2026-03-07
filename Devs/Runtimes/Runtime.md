---
related:
  - "[[Devs/Toolchains/Toolchain|Toolchain]]"
  - "[[Devs/Runtimes/C++/UCRT|UCRT]]"
  - "[[Devs/Runtimes/DotNet/CLR|CLR]]"
---

프로그램이 실행(Execution)되는 동안 베이스가 되는 소프트웨어 환경을 의미함.

## 주요 역할
- **Memory Management**: 스택 및 힙 메모리 할당, 가비지 컬렉션(GC) 수행.
- **OS Interaction**: 파일 I/O, 네트워크 통신, 시스템 콜(System Call) 중계.
- **Exception Handling**: 실행 중 발생하는 오류 처리 및 복구.
- **Standard Library**: 언어별 표준 함수 및 데이터 구조 제공.

## 실행 방식에 따른 분류
- **Library-based (C/C++)**: 컴파일 시점에 라이브러리가 정적/동적으로 링크되며, 하드웨어에서 직접 실행됨. (예: [[Devs/Runtimes/C++/UCRT|UCRT]], [[Devs/Runtimes/C++/MSVCRT|MSVCRT]])
- **Virtual Machine (C#, Java)**: 바이트코드로 컴파일된 후 VM 위에서 JIT(Just-In-Time) 컴파일을 통해 실행됨. (예: [[Devs/Runtimes/DotNet/CLR|CLR]], JVM)
- **Interpreted (Python, JS)**: 소스 코드나 바이트코드를 인터프리터가 한 줄씩 해석하며 실행함. (예: [[Devs/Runtimes/Python/Python VM|Python VM]])
