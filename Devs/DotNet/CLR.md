---
tags: [runtime, dotnet]
related:
  - "[[Devs/DotNet/Roslyn|Roslyn]]"
  - "[[Devs/Generals/Runtime|Runtime]]"
---

**Common Language Runtime**의 약자로, .NET 애플리케이션이 실행되는 가상 머신(VM) 환경임.

## 주요 역할
- **JIT Compilation**: IL 코드를 실행 직전에 실제 하드웨어 기계어로 변환함.
- **Garbage Collection (GC)**: 자동 메모리 관리를 통해 사용되지 않는 개체를 해제함.
- **Type Safety**: 실행 시 강력한 타입 체크를 수행하여 안정성을 확보함.
- **Exception Handling**: 여러 .NET 언어 간 일관된 예외 처리 메커니즘 제공.

## 구조
- **ClassLoader**: 어셈블리를 로드하고 레이아웃을 결정함.
- **Execution Engine**: 실제 코드 실행 및 리소스 관리를 담당함.
