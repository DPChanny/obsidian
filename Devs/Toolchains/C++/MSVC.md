---
related:
  - "[[Devs/Runtimes/C++/UCRT|UCRT]]"
  - "[[Devs/Toolchains/Toolchain|Toolchain]]"
---

Microsoft Visual C++ Toolchain의 약칭으로, Windows 네이티브 환경 빌드를 위한 공식 도구 모음임.

## 주요 도구
- **cl.exe**: C/C++ 컴파일러.
- **link.exe**: 오브젝트 파일을 라이브러리 및 런타임과 연결하는 링커.
- **nmake.exe**: Microsoft 버전의 빌드 관리 도구 (Make).
- **vcvarsall.bat**: MSVC 도구를 사용하기 위한 환경 변수 설정 스크립트.

## 특징
- **Windows SDK 의존성**: Windows API 호출을 위한 헤더 및 라이브러리가 Windows SDK와 긴밀하게 연결됨.
- **Runtime 지원**: 기본적으로 최신 버전의 경우 [[Devs/Runtimes/C++/UCRT|UCRT]]를 타겟팅함.
- **PDB (Program Database)**: 디버깅 정보를 담는 별도의 `.pdb` 파일을 생성하여 디버깅 효율을 높임.
