---
related:
---

## 1. 역할

브랜치를 전환하거나 새로운 브랜치를 생성하는 데 특화된 명령어임.

## 2. 주요 사용 사례

### 2.1. 브랜치 전환

- `git switch <branch-name>`: 기존에 존재하는 브랜치로 이동함.
- `git switch -`: 직전에 있었던 브랜치로 즉시 돌아감.

### 2.2. 브랜치 생성 및 전환

- `git switch -c <name>`: 새 브랜치를 생성하고 즉시 이동함.
- `git switch -c <name> <start-point>`: 특정 지점(커밋, 태그 등)에서 새 브랜치를 파생함.

## 3. 원격 브랜치 자동 추적

`git fetch` 이후 원격에만 존재하는 브랜치로 전환할 때의 동작:

1. `git fetch origin`을 통해 원격 추적 브랜치(`origin/feature-A`) 정보를 확보함.
2. `git switch feature-A` 실행 시 로컬에 해당 브랜치가 없으면 원격 추적 브랜치를 검색함.
3. 동일 이름의 원격 브랜치가 존재하면 이를 추적하는 로컬 브랜치를 **자동 생성**하고 전환함.

## 4. 문법 요약

```bash
# 기본 전환
git switch main

# 생성 후 전환
git switch -c feature/new-api
```
