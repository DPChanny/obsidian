---
tags: []
related:
  - "[[MSVCRT|MSVCRT]]"
  - "[[UCRT|UCRT]]"
---

**Minimalist GNU for Windows**의 약자로, GCC(GNU Compiler Collection)를 Windows 환경에서 네이티브로 사용할 수 있도록 포팅한 툴체인임.

## Toolchain 구성

### Compiler

- **GCC (`gcc`, `g++`)**: GNU 프로젝트의 C/C++ 컴파일러.

  ```bash
  g++ main.cpp -o main.exe
  ```

### Linker

- **GNU Linker (`ld`)**: 오브젝트 파일과 라이브러리를 결합함.

  ```bash
  gcc main.o -L./lib -lhelper -o main.exe
  ```

### Debugger

- **GDB (`gdb`)**: 오픈 소스 프로젝트의 표준 디버거.

  ```bash
  gdb main.exe
  ```

## 특징

- **Native Performance**: 별도의 POSIX 에뮬레이션 레이어 없이 Windows API를 직접 호출함.
- **MinGW-w64**: 64비트 지원 및 향상된 Windows SDK 호환성을 위해 기존 MinGW에서 분기된 프로젝트임.
- **Cross-Compilation**: 리눅스 등 다른 OS에서 Windows용 바이너리를 생성하는 데 널리 쓰임.
