---
related:
  - "[[Devs/Toolchains/Toolchain|Toolchain]]"
  - "[[Devs/Runtimes/C++/UCRT|UCRT]]"
  - "[[Devs/Runtimes/Python/Python VM|Python VM]]"
---

프로그램이 실행(Execution)되는 동안 베이스가 되는 소프트웨어 환경을 의미함. 런타임은 컴파일된 바이너리가 실제 하드웨어 자원을 효율적으로 사용할 수 있도록 인터페이스를 제공함.

## 주요 역할 (Primary Roles)

### 1. Memory Management (메모리 관리)
프로세스가 사용하는 메모리 영역을 할당하고 회수하는 역할을 함.
- **Stack & Heap**: 메모리 레이아웃 결정 및 동적 할당 관리.
- **Garbage Collection (GC)**: 더 이상 참조되지 않는 객체를 자동 탐지하여 메모리를 해제함. (C#, Python)

### 2. OS Interaction (OS 인터랙션)
운영체제의 기능을 추상화하여 제공함.
- **System Call**: 파일 I/O, 네트워크 통신, 프로세스 제어 등을 수행함.
- **Standard Library**: 문자열 처리, 수학 연산 등 필수적인 기능 집합을 제공함.

### 3. Execution Engine (실행 엔진)
소스 코드나 바이트코드를 실제 실행 코드로 변환하고 처리함.
- **Interpreting**: 바이트코드를 한 줄씩 해석하며 즉각 실행함.
- **JIT Compilation**: 실행 시점에 자주 사용되는 코드를 기계어로 즉석 컴파일하여 성능을 높임.

## 실행 방식에 따른 분류

| 실행 방식 | 특징 | 주요 사례 |
| :--- | :--- | :--- |
| **Library-based** | 라이브러리가 바이너리에 포함되어 직접 실행됨. | `UCRT`, `MSVCRT` (C/C++) |
| **Virtual Machine** | 바이트코드를 VM 위에서 실행 및 JIT 컴파일함. | `CLR` (.NET), `JVM` (Java) |
| **Interpreter** | 소스나 바이트코드를 인터프리터가 즉시 해석함. | `Python VM`, `JS Engine` |

## 런타임과 하드웨어의 관계
1. **Source Code**: 개발자가 작성한 코드.
2. **Intermediate Code**: 컴파일러가 생성한 중간 표현 (IR, Bytecode).
3. **Runtime Environment**: 가상 머신 또는 런타임 라이브러리.
4. **Machine Code**: CPU가 실행 가능한 최종 명령어.
5. **Hardware**: 실제 연산 수행.

런타임은 이 과정에서 **Source**와 **Hardware** 사이의 추상화 계층으로 동작하며, 실행 시점의 안정성과 성능을 보장하는 핵심 역할을 담당함.
