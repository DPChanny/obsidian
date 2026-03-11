---
related:
  - "[[Devs/Stacks/Tools/Git/Git Convention/Git Convention|Git Convention]]"
---

### 1. 브랜치 파생 원칙 (Rule 1)

**정의**: 각 브랜치는 반드시 지정된 기준 브랜치(Merge-base)에서만 생성함.

**목적**: 병합 충돌 방지 및 안정적인 merge-base 유지함.

- `/feature/` → `/develop/`에서 파생함.
- `/develop/` → `/release/`에서 파생함.
- `/release/` → `develop/`에서 파생함.
- `develop/` → `release/`에서 파생함.
- `release/` → `master`에서 파생함.

---

### 2. 단방향 병합 원칙 (Rule 2)

**정의**: 병합은 파생된 방향의 역순으로만 수행함.

**금지**: 역방향 병합(예: develop → feature, release → develop 등)은 엄격히 금지함.

- `/feature/` → `/develop/`
- `/develop/` → `/release/`
- `/release/` → `develop/`
- `develop/` → `release/`
- `release/` → `master`

---

### 3. 브랜치 폐기 원칙 (Rule 3)

**정의**: 병합이 완료된 하위 브랜치는 즉시 삭제함.

**이유**: 브랜치 재사용 시 merge-base 혼동으로 인한 중복 이력 및 충돌 발생함.

- `/feature/`: 병합 후 즉시 폐기하며, 후속 작업 시 다음 버전(`v{next}`)으로 재생성함.
- `/develop/`, `/release/`: 병합 후 폐기하며, 새로운 기준선이 필요할 때 재생성함.
- `hotfix/*`: 해결 후 즉시 폐기함.

---

### 4. 핵심 메커니즘 요약

- 모든 브랜치는 일회성임.
- 각 단계가 완료된 순간 브랜치의 수명을 종료함.
- 연계되는 작업은 반드시 최신화된 상위 브랜치에서 새로운 기준선(Version)을 가지고 시작하도록 강제함.
