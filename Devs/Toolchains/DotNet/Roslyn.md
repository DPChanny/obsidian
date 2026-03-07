---
tags: [toolchain, dotnet]
related:
  - "[[Devs/Runtimes/DotNet/CLR|CLR]]"
  - "[[Devs/Toolchains/Toolchain|Toolchain]]"
---

Microsoft에서 개발한 오픈 소스 .NET 컴파일러 플랫폼으로, C# 및 Visual Basic 코드를 분석하고 IL(Intermediate Language)로 컴파일함.

## 주요 기능
- **Compiler APIs**: 소스 코드를 구문 트리(Syntax Tree)로 변환하고 시맨틱 분석을 수행함.
- **Code Analysis**: 정적 분석 및 리팩토링 도구를 위한 API를 제공함.
- **Emit API**: 메모리 내에서 직접 어셈블리(.dll, .exe)를 생성함.

## 특징
- **C# as a Service**: 컴파일러 내부를 API로 공개하여 IDE(Visual Studio 등) 개발에 활용됨.
- **Output**: 기계어가 아닌 플랫폼 독립적인 **CIL (Common Intermediate Language)**을 생성함.
