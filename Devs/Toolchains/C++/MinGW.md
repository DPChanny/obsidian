---
related:
  - "[[Devs/Toolchains/C++/MSYS2|MSYS2]]"
  - "[[Devs/Runtimes/C++/UCRT|UCRT]]"
  - "[[Devs/Runtimes/C++/MSVCRT|MSVCRT]]"
---

**Minimalist GNU for Windows**의 약자로, GCC(GNU Compiler Collection)를 Windows 환경에서 네이티브로 사용할 수 있도록 포팅한 툴체인임.

## 특징
- **Native Performance**: 별도의 POSIX 에뮬레이션 레이어 없이 Windows API를 직접 호출함.
- **MinGW-w64**: 64비트 지원 및 향상된 Windows SDK 호환성을 위해 기존 MinGW에서 분기된 프로젝트임.
- **Cross-Compilation**: 리눅스 등 다른 OS에서 Windows용 바이너리를 생성하는 데 널리 쓰임.

## Runtime 선택
- **MSVCRT 기반**: 레거시 환경과의 호환성을 중시함.
- **UCRT 기반**: 최신 C 표준 준수 및 현대적 Windows 환경에 최적화됨. (MSYS2 UCRT64 환경)
