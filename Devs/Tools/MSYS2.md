---
related:
  - "[[Devs/Toolchains/C++/MinGW|MinGW]]"
  - "[[Devs/Runtimes/C++/UCRT|UCRT]]"
---

Windows 환경에서 POSIX 유사 환경(Bash, 패키지 관리자 등)을 제공하며, 다양한 툴체인과 런타임을 관리하는 소프트웨어 배포판임.

## 핵심 기능
- **Pacman**: Arch Linux 기반의 패키지 관리 시스템을 통해 라이브러리와 도구를 손쉽게 설치함.
- **Subsystems**: 서로 다른 런타임과 툴체인 조합을 가진 독립적인 환경을 제공함.

## Toolchain 구성 (UCRT64 기준)
- **Compiler**: `gcc`, `clang`
- **Linker**: `ld`, `lld`
- **Debugger**: `gdb`, `lldb`

## 주요 환경 (Environments)
| 환경 명칭 | Toolchain | C Runtime (CRT) | 특징 |
| :--- | :--- | :--- | :--- |
| **UCRT64** | GCC/Clang | [[Devs/Runtimes/C++/UCRT|UCRT]] | Windows 10/11 표준, 권장 환경 |
| **MINGW64** | GCC | [[Devs/Runtimes/C++/MSVCRT|MSVCRT]] | 레거시 호환성 중심 |
| **CLANG64** | Clang | [[Devs/Runtimes/C++/UCRT|UCRT]] | LLVM/Clang 최적화 환경 |

## 사용 예시
```bash
# UCRT64 환경에서 GCC 설치
pacman -S mingw-w64-ucrt-x86_64-gcc

# 패키지 업데이트
pacman -Syu
```
