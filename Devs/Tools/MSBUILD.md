---
related:
  - "[[Devs/Toolchains/C++/MSVC|MSVC]]"
---

Microsoft Build Engine의 약칭으로, .NET 및 C++ 프로젝트 빌드를 위한 오픈 소스 빌드 플랫폼임.

## 주요 구성 요소
- **Project File (.csproj, .vcxproj)**: 빌드 대상, 종속성, 빌드 설정을 XML 형식으로 정의함.
- **Targets**: 빌드 과정에서 수행할 작업의 집합.
- **Tasks**: 실제 빌드 작업을 수행하는 실행 코드 단위.

## Toolchain 구성
### Compiler
- **CL (`cl.exe`) / CSC (`csc.exe`)**: C++ 및 C# 컴파일러를 호출함.
  ```powershell
  msbuild MyProject.sln /p:Configuration=Release
  ```
### Linker
- **LINK (`link.exe`)**: 프로젝트 파일의 설정에 따라 링커를 실행함.
  ```powershell
  msbuild MyProject.vcxproj /t:Link
  ```
### Debugger
- **Visual Studio Debugger**: 빌드된 결과물을 디버깅하기 위해 호출됨.
  ```powershell
  msbuild MyProject.sln /t:Build /p:Optimize=false
  ```

## 사용 예시
```powershell
# 특정 타겟 실행 (Clean 후 Build)
msbuild MyProject.csproj /t:Clean,Build
```
