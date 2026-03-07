---
related:
---

## 1. 역할
로컬 저장소의 브랜치를 생성, 목록 확인, 이름 변경, 삭제 등 관리하는 명령어임.

## 2. 주요 사용 사례

### 2.1. 목록 확인
- `git branch`: 로컬 브랜치 목록을 표시함.
- `git branch -a`: 로컬 및 원격의 모든 브랜치를 표시함.
- `git branch -v`: 마지막 커밋 정보를 포함하여 표시함.

### 2.2. 브랜치 생성 및 변경
- `git branch <name>`: 현재 HEAD 기준으로 새 브랜치를 생성함.
- `git branch -m <old> <new>`: 브랜치 이름을 변경함.

### 2.3. 브랜치 삭제
- `git branch -d <name>`: 병합된 브랜치를 안전하게 삭제함.
- `git branch -D <name>`: 병합 여부와 관계없이 강제로 삭제함.

## 3. 문법 요약
```bash
# 상세 목록 확인
git branch -av

# 안전 삭제
git branch -d feature/old
```
