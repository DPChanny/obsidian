---
related:
---

## 1. 역할
원격 저장소의 최신 변경 사항을 가져와 로컬 브랜치에 즉시 병합함.

## 2. 동작 방식

### 2.1. Merge 방식 (Default)
- `git fetch`와 `git merge`를 결합한 동작임.
- 원격의 이력을 로컬 이력 뒤에 병합 커밋으로 생성함.

### 2.2. Rebase 방식
- `git pull --rebase` 옵션을 사용함.
- 로컬 커밋들을 원격의 최신 커밋 위로 재배치하여 히스토리를 직선화함.

## 3. 문법 요약
```bash
# 기본 pull
git pull origin main

# Rebase 기반 pull
git pull --rebase origin dev
```
