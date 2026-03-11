---
tags: []
related:
  - "[[Devs/Languages/C++/Runtimes/MSVCRT|MSVCRT]]"
  - "[[Devs/Languages/C++/Toolchains/MSVC|MSVC]]"
---

**Universal C Runtime**의 약자로, Windows 10 이후 OS의 핵심 구성 요소로 포함된 최신 C 표준 라이브러리임.

## 특징

- **OS 구성 요소**: `ucrtbase.dll`로 구현되며, OS 업데이트를 통해 최신 표준이 유지됨.
- **버전 독립성**: 특정 컴파일러 버전에 종속되지 않는 공용 런타임 환경 제공.
- **표준 준수**: C99, C11 등 최신 C 표준 기능을 폭넓게 지원함.

## MSYS2와의 관계

- **UCRT64 환경**: MSYS2에서 GCC/Clang을 사용하여 UCRT를 타겟으로 빌드할 때 사용하는 환경임.
- **장점**: MSVC로 빌드된 라이브러리와의 바이너리 호환성이 높고, 최신 Windows 기능 활용에 유리함.
