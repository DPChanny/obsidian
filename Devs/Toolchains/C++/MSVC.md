---
related:
  - "[[Devs/Runtimes/C++/UCRT|UCRT]]"
  - "[[Devs/Tools/MSBUILD|MSBUILD]]"
---

Microsoft Visual C++ Toolchain의 약칭으로, Windows 네이티브 환경 빌드를 위한 공식 도구 모음임.

## Toolchain 구성
### Compiler
- **CL (`cl.exe`)**: Microsoft C/C++ 컴파일러.
  ```powershell
  cl main.cpp /Fe:main.exe /Zi
  ```
### Linker
- **LINK (`link.exe`)**: 오브젝트 파일과 런타임을 연결하여 바이너리를 생성함.
  ```powershell
  link main.obj /OUT:main.exe user32.lib
  ```
### Debugger
- **Visual Studio Debugger**: 강력한 GUI 디버깅 환경 제공.
  ```powershell
  vsjitdebugger.exe main.exe
  ```

## 주요 도구
- **nmake.exe**: Microsoft 버전의 빌드 관리 도구 (Make).
- **vcvarsall.bat**: MSVC 도구를 사용하기 위한 환경 변수 설정 스크립트.

## 특징
- **Windows SDK 의존성**: Windows API 호출을 위한 헤더 및 라이브러리가 Windows SDK와 긴밀하게 연결됨.
- **PDB (Program Database)**: 디버깅 정보를 담는 별도의 `.pdb` 파일을 생성하여 디버깅 효율을 높임.
