---
related:
  - "[[Devs/Toolchains/C++/MSVC|MSVC]]"
---

Microsoft Build Engine의 약칭으로, .NET 및 C++ 프로젝트 빌드를 위한 오픈 소스 빌드 플랫폼임.

## 주요 구성 요소
- **Project File (.csproj, .vcxproj)**: 빌드 대상, 종속성, 빌드 설정을 XML 형식으로 정의함.
- **Targets**: 빌드 과정에서 수행할 작업의 집합 (예: Compile, Link, Copy).
- **Tasks**: 실제 빌드 작업을 수행하는 실행 코드 단위.

## Toolchain 구성
- **Compiler**: `cl.exe` (C++), `csc.exe` (C#)
- **Linker**: `link.exe`
- **Debugger**: `vsjitdebugger.exe`, Visual Studio Debugger

## 사용 예시
```powershell
# 프로젝트 빌드 (Release 모드)
msbuild MyProject.sln /p:Configuration=Release

# 특정 타겟 실행 (Clean 후 Build)
msbuild MyProject.csproj /t:Clean,Build
```
