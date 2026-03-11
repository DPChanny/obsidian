---
tags: []
related:
  - "[[Devs/Stacks/Git/Git Convention/Git Convention|Git Convention]]"
---

### 1. Naming Format

| 브랜치 유형 | 네이밍 형식 |
|:--- |:--- |
| 기능 브랜치 | `*/feature/{feature_name}/{feature_version}` |
| 팀별 개발 브랜치 | `*/develop/{develop_version}` |
| 팀별 릴리즈 브랜치 | `*/release/{release_version}` |
| 전체 개발 브랜치 | `develop/{develop_version}` |
| 전체 릴리즈 브랜치 | `release/{release_version}` |
| 핫픽스 브랜치 | `hotfix/{hotfix_name}/{hotfix_version}` |

- 모든 버전 필드(`{..._version}`)는 `v{major}.{minor}.{patch}` 형식을 따름.

---

### 2. Versioning Rules

#### feature_version

- 해당 feature 브랜치가 `/develop/{develop_version}`에 병합된 이후, 동일 기능에 대한 후속 작업을 수행할 때 증가함.
- 예: `frontend/feature/login/v1.0.0` 병합 후 `frontend/feature/login/v1.1.0` 생성함.

#### develop_version

- 이전 develop 브랜치 병합 및 폐기 후, 새로운 작업 기준선이 필요할 때 증가함.
- 각 파트(`frontend/`, `ai/`)와 전체(`develop/`) 브랜치는 독립적인 버전을 유지함.

#### release_version

- develop 브랜치의 기능이 안정화되어 릴리즈 준비 단계로 진입할 때 부여함.

#### hotfix_version

- **`release/{release_version}` 브랜치에서만 파생되어야 함.**
- 팀별 릴리즈 브랜치(`/release/`)에서의 파생은 금지함.

---

### 3. 버전 간 독립성 원칙

- 각 파트의 개발/릴리즈 브랜치는 독립된 버전 체계를 가짐.
- 전체 개발/릴리즈 브랜치(`develop/`, `release/`)는 각 파트 브랜치와 별개임. (계층적 상하 관계 아님)

---

### 4. Naming Constraints

- 중괄호(`{}`) 내부 구조 및 순서 유지는 필수이며 생략 불가함.
- `{feature_name}`, `{hotfix_name}`은 의미 기반으로 명명하며, 중복 방지를 위해 팀명/도메인 접두어 사용 가능함.
- 브랜치명 내 구분자는 언더스코어(`_`) 대신 슬래시(`/`)를 사용함.
