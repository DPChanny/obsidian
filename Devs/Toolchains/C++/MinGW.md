---
related:
  - "[[Devs/Tools/MSYS2|MSYS2]]"
  - "[[Devs/Runtimes/C++/UCRT|UCRT]]"
  - "[[Devs/Runtimes/C++/MSVCRT|MSVCRT]]"
---

**Minimalist GNU for Windows**의 약자로, GCC(GNU Compiler Collection)를 Windows 환경에서 네이티브로 사용할 수 있도록 포팅한 툴체인임.

## Toolchain 구성
- **Compiler**: `gcc`, `g++`
- **Linker**: `ld`
- **Debugger**: `gdb`

## 특징
- **Native Performance**: 별도의 POSIX 에뮬레이션 레이어 없이 Windows API를 직접 호출함.
- **MinGW-w64**: 64비트 지원 및 향상된 Windows SDK 호환성을 위해 기존 MinGW에서 분기된 프로젝트임.
- **Cross-Compilation**: 리눅스 등 다른 OS에서 Windows용 바이너리를 생성하는 데 널리 쓰임.

## 사용 예시
```bash
# C++ 소스 코드 컴파일
g++ main.cpp -o main.exe

# 정적 라이브러리와 함께 컴파일
gcc main.c -L./lib -lmyhelper -o main.exe
```
