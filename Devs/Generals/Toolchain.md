---
tags: [concept]
related:
  - "[[Devs/Generals/Runtime|Runtime]]"
  - "[[Devs/C/MSYS2|MSYS2]]"
---

소스 코드를 실행 가능한 바이너리(Executable) 제품으로 변환하기 위해 연쇄적으로 사용되는 개발 도구의 집합임.

## 주요 구성 요소
- **Compiler**: 고수준 언어(C++, C#, Python 등) 소스 코드를 저수준 코드(Object Code, Bytecode)로 변환함. (예: `cl.exe`, `gcc`, `csc.exe`)
- **Linker**: 여러 개의 오브젝트 파일과 라이브러리를 결합하여 하나의 실행 파일이나 라이브러리를 생성함. (예: `link.exe`, `ld`)
- **Assembler**: 어셈블리 코드를 기계어로 변환함.
- **Debugger**: 실행 중인 프로그램의 오류를 추적하고 메모리 상태를 검사함.

## 빌드 프로세스 (C 기준)
1. **Preprocessing**: 매크로 치환 및 헤더 파일 포함.
2. **Compilation**: 소스 코드를 어셈블리 코드로 변환.
3. **Assembly**: 어셈블리 코드를 기계어 오브젝트 파일로 변환.
4. **Linking**: 오브젝트 파일들을 표준 라이브러리와 결합하여 최종 바이너리 생성.

## 주요 툴체인 분류
- **Native Toolchain**: 실행 중인 OS/아키텍처와 동일한 타겟의 바이너리를 생성함.
- **Cross Toolchain**: 현재 환경과 다른 OS/아키텍처(예: Windows에서 Android용) 바이너리를 생성함.
