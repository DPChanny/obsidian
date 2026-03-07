---
tags:
  - runtime
  - c
  - windows
related:
  - "[[Devs/Runtimes/C++/UCRT|UCRT]]"
  - "[[Devs/Toolchains/C++/MinGW|MinGW]]"
---

**Microsoft Visual C++ Runtime**의 레거시 버전(msvcrt.dll)을 의미하며, 이전 세대 Windows 환경의 기본 C 표준 라이브러리임.

## 특징
- **Legacy Compatibility**: 오래된 Windows 버전에서도 별도의 설치 없이 동작함.
- **Standard Support**: C99 이후의 최신 C 표준 지원이 미흡함 (특히 `printf`, `scanf` 관련 동작).
- **Static Linking**: 버전별 파편화 문제를 해결하기 위해 정적 링크를 자주 사용함.

## 현대적 위상
- 현재는 [[Devs/Runtimes/C++/UCRT|UCRT]]로 대체되는 추세이며, MSYS2의 **MINGW64** 환경에서 호환성 목적으로 주로 사용됨.
