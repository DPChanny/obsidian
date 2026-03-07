---
---
### 1. Naming Format

|브랜치 유형|네이밍 형식|
|---|---|
|기능 브랜치|`*/feature/{feature_name}/{feature_version}`|
|팀별 개발 브랜치|`*/develop/{develop_version}`|
|팀별 릴리즈 브랜치|`*/release/{release_version}`|
|전체 개발 브랜치|`develop/{develop_version}`|
|전체 릴리즈 브랜치|`release/{release_version}`|
|핫픽스 브랜치|`hotfix/{hotfix_name}/{hotfix_version}`|

모든 버전 필드 (`{..._version}`)는 반드시 `v{major}.{minor}.{patch}` 형식을 사용합니다.

---

### 2. Versioning Rules

### {feature_version}

- 형식: `v{major}.{minor}.{patch}`
- 증가 조건:
    - 해당 feature 브랜치가 `/develop/{develop_version}`에 병합된 이후, 같은 기능명으로 작업을 계속하는 경우
- 예:
    - `frontend/feature/login/v1.0.0` → 병합 후 폐기
    - `frontend/feature/login/v1.1.0` → 후속 작업

### {develop_version}

- 형식: `v{major}.{minor}.{patch}`
- 적용 대상: `/develop/{develop_version}`, `develop/{develop_version}`
- 증가 조건:
    - 이전 develop 브랜치 병합 및 폐기 이후, 새 작업 기준선이 필요할 경우
- 예:
    - `frontend/develop/v1.0.0`, `ai/develop/v0.3.0`, `develop/v2.0.0` (각기 독립)

### {release_version}

- 형식: `v{major}.{minor}.{patch}`
- 적용 대상: `/release/{release_version}`, `release/{release_version}`
- 증가 조건:
    - 해당 develop 브랜치에서 기능이 안정화되어 릴리즈 준비가 시작될 경우
- 예:
    - `frontend/release/v1.0.0`, `ai/release/v1.0.0`, `release/v2.1.0`

### {hotfix_version}

- 형식: `v{major}.{minor}.{patch}`
- 사용 조건:
    - **항상 `release/{release_version}` 브랜치에서만 파생되어야 함**
    - 팀별 릴리즈 브랜치(`/release/{...}`)에서는 hotfix 브랜치 생성 금지
- 예:
    - `hotfix/payment-crash/v1.0.0`

---

### 3. 버전 간 독립성 원칙

- **각 파트의 개발/릴리즈 브랜치는 독립된 버전 체계를 갖는다.**
    - `frontend/develop/v1.0.0`과 `ai/develop/v1.0.0`은 서로 관계없다.
- **전체 개발/릴리즈 브랜치 (`develop/`, `release/`) 또한 각 파트 브랜치와 완전히 별개이다.**
    - `release/v1.0.0`은 `frontend/release/v1.0.0`의 상위 버전이 아님

---

### 4. Naming Constraints

- `{}` 내부 구조와 순서는 반드시 유지해야 하며 생략이나 대체 불가
- `{feature_name}`, `{hotfix_name}` 등은 의미 기반으로 명명하고, 중복 방지를 위해 팀명이나 도메인을 사용할 수 있음
- 브랜치명에는 `_` 대신 `/`를 사용

---

이 전략은 버전 정보와 명명 규칙을 통해 브랜치 기준을 명확히 선언함으로써,

협업 중 병합 기준 혼란을 예방하고 이력 추적을 구조적으로 단순화합니다.