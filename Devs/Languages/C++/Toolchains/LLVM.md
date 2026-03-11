---
tags: []
related: 
---

컴파일러 인프라스트럭처 프로젝트로, 모듈화된 재사용 가능한 컴파일러 및 툴체인 기술의 집합임.

## Toolchain 구성

### Compiler

- **Clang (`clang`, `clang++`)**: C/C++ 소스 코드를 LLVM IR 또는 기계어로 컴파일함.

  ```bash
  clang++ main.cpp -o main.exe -O2
  ```

### Linker

- **LLD (`lld`)**: 여러 오브젝트 파일을 결합하여 최종 실행 파일을 생성함.

  ```bash
  clang++ main.obj -fuse-ld=lld -o main.exe
  ```

### Debugger

- **LLDB (`lldb`)**: 실행 중인 프로그램을 분석하고 오류를 추적함.

  ```bash
  lldb main.exe
  ```

## 주요 도구

- **LLVM IR**: 고수준 언어와 기계어 사이의 중간 표현 형식.

## 특징

- **모듈화**: 각 컴파일 단계가 독립적인 라이브러리로 구성되어 재사용성이 높음.
- **최적화**: 강력한 전역 최적화 및 링크 타임 최적화(LTO)를 제공함.
