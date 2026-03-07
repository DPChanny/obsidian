---
related:
  - "[[Devs/Runtimes/C++/UCRT|UCRT]]"
  - "[[Devs/Tools/MSBUILD|MSBUILD]]"
---

Microsoft Visual C++ Toolchain의 약칭으로, Windows 네이티브 환경 빌드를 위한 공식 도구 모음임.

## Toolchain 구성
- **Compiler**: `cl.exe`
- **Linker**: `link.exe`
- **Debugger**: `vsjitdebugger.exe`, Visual Studio Debugger

## 주요 도구
- **nmake.exe**: Microsoft 버전의 빌드 관리 도구 (Make).
- **vcvarsall.bat**: MSVC 도구를 사용하기 위한 환경 변수 설정 스크립트.

## 특징
- **Windows SDK 의존성**: Windows API 호출을 위한 헤더 및 라이브러리가 Windows SDK와 긴밀하게 연결됨.
- **PDB (Program Database)**: 디버깅 정보를 담는 별도의 `.pdb` 파일을 생성하여 디버깅 효율을 높임.

## 사용 예시
```powershell
# 환경 변수 로드 후 컴파일 및 링크
vcvarsall.bat x64
cl main.cpp /Fe:main.exe /Zi

# MSVC 링커를 명시적으로 호출
link main.obj /OUT:main.exe user32.lib
```
