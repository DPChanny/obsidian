---
related:
---

컴파일러 인프라스트럭처 프로젝트로, 모듈화된 재사용 가능한 컴파일러 및 툴체인 기술의 집합임.

## 주요 구성 요소
- **Clang**: C, C++, Objective-C 등을 위한 LLVM 기반 프론트엔드 컴파일러.
- **LLD**: 고성능 링커.
- **LLDB**: 고성능 디버거.
- **LLVM IR**: 고수준 언어와 기계어 사이의 중간 표현 형식.

## Toolchain 구성
- **Compiler**: `clang`, `clang++`
- **Linker**: `lld`
- **Debugger**: `lldb`

## 특징
- **모듈화**: 각 컴파일 단계가 독립적인 라이브러리로 구성되어 재사용성이 높음.
- **최적화**: 강력한 전역 최적화 및 링크 타임 최적화(LTO)를 제공함.

## 사용 예시
```bash
# C++ 파일 컴파일 및 실행 파일 생성 (최적화 O2)
clang++ main.cpp -o main.exe -O2

# LLVM IR 생성 (비트코드 형식)
clang -S -emit-llvm main.c -o main.ll
```
