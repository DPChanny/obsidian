---
---
### Rule 1: 브랜치는 반드시 정의된 기준 브랜치에서만 파생되어야 한다

**이유:**

Git은 병합 시 기준이 되는 공통 조상(merge-base)을 기반으로 병합을 수행합니다. 브랜치를 임의의 커밋에서 파생하면 merge-base가 불안정해지고, 병합 충돌과 이력 오염이 발생합니다.

**따라서 아래 규칙을 반드시 따라야 합니다.**

- 각 `/feature/{feature_name}/{feature_version}`는 반드시 `/develop/{develop_version}`에서 파생되어야 한다
    
    예: `frontend/feature/login/v1` → `frontend/develop/v1`
    
- 각 `/develop/{develop_version}`는 반드시 `/release/{release_version}`에서 파생되어야 한다
    
    예: `frontend/develop/v1` → `frontend/release/v1.0.0`
    
- 각 `/release/{release_version}`는 반드시 `develop/{develop_version}`에서 파생되어야 한다
    
    예: `frontend/release/v1.0.0` → `develop/v1`
    
- 각 `develop/{develop_version}`는 반드시 `release/{release_version}`에서 파생되어야 한다
    
    예: `develop/v1` → `release/v1.0.0`
    
- 각 `release/{release_version}`는 반드시 `master`에서 파생되어야 한다
    
    예: `release/v1.0.0` → `master`
    

> 이 구조를 따르면 병합 기준선이 고정되며, Git은 항상 명확한 기준을 가지고 병합을 수행할 수 있습니다.

---

### Rule 2: 병합은 파생된 브랜치로만 단방향으로 이루어져야 한다

**이유:**

양방향 병합은 merge-base를 꼬이게 하고, 병합 충돌과 중복 이력을 유발합니다.

**따라서 병합은 항상 브랜치가 파생된 기준 브랜치로만 수행되어야 하며, 역방향 병합은 허용되지 않습니다.**

- `/feature/{feature_name}/{feature_version}` → `/develop/{develop_version}`
- `/develop/{develop_version}` → `/release/{release_version}`
- `/release/{release_version}` → `develop/{develop_version}`
- `release/{release_version}` → `master`

> 상위 브랜치에서 하위 브랜치로의 병합(develop → feature, release → develop 등)은 금지합니다.

---

### Rule 3: 병합이 완료된 하위 브랜치는 무조건 폐기되어야 한다

**이유:**

하위 브랜치를 상위 브랜치에 병합한 후, 작업을 이어서 계속하고 싶어지는 경우가 많습니다.

예를 들어:

- `/feature/{feature_name}/{feature_version}`를 `/develop/{develop_version}`에 병합한 뒤 다시 작업을 이어가거나
- `/develop/{develop_version}`을 다시 해당 feature 브랜치에 병합하려는 경우
- `/release/{release_version}`을 병합한 후 그 브랜치에서 hotfix를 이어가려는 경우

그러나 이와 같은 브랜치 재사용은 **Rule 2(단방향 병합)** 원칙을 위배합니다.

Git은 병합 기준(merge-base)을 혼동하게 되고, 중복 이력, 충돌, 불명확한 리뷰 대상 등의 문제가 발생합니다.

**따라서 병합이 완료된 브랜치는 무조건 폐기하며, 이후 작업은 항상 새로운 브랜치로 시작해야 합니다.**

이 원칙은 다음과 같이 적용됩니다:

- `/feature/{feature_name}/{feature_version}` → 병합 후 즉시 폐기
    - 후속 작업은 `/feature/{feature_name}/{next_feature_version}`에서 시작
- `/develop/{develop_version}` → 병합 후 폐기
    - 후속 개발은 `/develop/{next_develop_version}`으로 시작
- `/release/{release_version}` → 병합 후 폐기
    - 다음 릴리즈는 `/release/{next_release_version}`으로 시작
- `hotfix/*` → 병합 후 폐기
    - 추가 핫픽스는 새로 시작

> 이 구조는 모든 브랜치에 대해 역할이 완료된 순간 종료하고, 이어지는 작업은 반드시 새로운 기준선에서 새롭게 시작하도록 강제합니다.
> 
> 이를 통해 병합 기준을 명확히 유지하고, 이력 오염과 충돌을 구조적으로 방지할 수 있습니다.