---
related:
  - "[[Devs/Languages/C++/Toolchains/LLVM|LLVM]]"
  - "[[Devs/Languages/C++/Toolchains/MinGW|MinGW]]"
  - "[[Devs/Languages/C++/Toolchains/MSVC|MSVC]]"
  - "[[Devs/Languages/Runtime|Runtime]]"
---

소스 코드를 실행 가능한 바이너리(Executable Binary) 제품으로 변환하기 위해 유기적으로 연결된 개발 도구들의 집합임. 툴체인은 단순한 도구의 나열이 아니라, 특정 플랫폼이나 아키텍처를 타겟팅하기 위한 일련의 공정(Pipeline)을 의미함.

## 핵심 구성 요소 (Core Components)

### 1. Compiler (컴파일러)

고수준 언어(C++, C#, Python 등) 소스 코드를 컴퓨터가 이해할 수 있는 저수준 코드(Object Code, Bytecode)로 변환함.

- **Frontend**: 언어 문법 분석 및 중간 표현(IR) 생성.
- **Backend**: 타겟 아키텍처(x86, ARM 등)에 최적화된 기계어 생성.
- **주요 구현**: `cl.exe` (MSVC), `gcc` (GNU), `clang` (LLVM).

### 2. Linker (링커)

여러 개의 오브젝트 파일과 외부 라이브러리를 결합하여 하나의 완성된 실행 파일이나 공유 라이브러리를 생성함.

- **Symbol Resolution**: 함수나 변수의 참조 위치를 확정함.
- **Static vs Dynamic**: 컴파일 시점에 포함할지(Static), 실행 시점에 로드할지(Dynamic) 결정함.
- **주요 구현**: `link.exe` (MSVC), `ld` (GNU), `lld` (LLVM).

### 3. Debugger (디버거)

실행 중인 프로그램의 내부 상태(메모리, 레지스터, 스택 등)를 검사하고 로직 오류를 추적함.

- **Breakpoints**: 특정 지점에서 실행을 일시 중단함.
- **Inspection**: 변수 값 확인 및 메모리 덤프 분석.
- **주요 구현**: `vsjitdebugger.exe` (Windows), `gdb` (GNU), `lldb` (LLVM).

## 주요 툴체인 생태계

| 생태계 | Compiler | Linker | Debugger | 주요 특징 |
|:--- |:--- |:--- |:--- |:--- |
| **MSVC** | `cl.exe` | `link.exe` | VS Debugger | Windows 표준, PDB 디버깅 정보 사용 |
| **GNU** | `gcc` | `ld` | `gdb` | POSIX 표준, 강력한 이식성 |
| **LLVM** | `clang` | `lld` | `lldb` | 모듈화된 구조, 고성능 최적화 |

## 빌드 프로세스 흐름 (C/C++ 기준)

1. **Preprocessing**: 매크로 확장 및 헤더 포함 (`#include`, `#define`).
2. **Compilation**: 소스 코드 → 어셈블리/오브젝트 파일 변환.
3. **Assembly**: 어셈블리 코드 → 기계어 변환.
4. **Linking**: 오브젝트 파일 + 표준 라이브러리 → 최종 실행 파일(`.exe`, `.so`).
