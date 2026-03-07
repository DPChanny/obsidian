---
related:
---

## 1. 역할

작업 디렉토리나 스테이징 영역의 파일을 특정 상태로 복원하는 전용 명령어임.

## 2. 주요 사용 사례

### 2.1. 작업 디렉토리 복원
- `git restore <file>`: 파일의 수정을 취소하고 마지막 커밋 또는 스테이징된 상태로 되돌림.
- `git restore .`: 현재 디렉토리의 모든 수정 사항을 폐기함.

### 2.2. 스테이징 해제 (Unstage)
- `git restore --staged <file>`: `git add` 된 파일을 인덱스에서 제거함. 실제 파일 내용은 유지됨.

### 2.3. 특정 소스에서 복원
- `git restore --source=<commit> <file>`: 과거 특정 시점이나 다른 브랜치의 파일 내용을 현재 작업 디렉토리로 가져옴.

## 3. 문법 요약

```bash
# 작업 디렉토리 복원 (Default)
git restore --worktree <file>

# 스테이징 영역 복원
git restore --staged <file>

# 특정 커밋 기준으로 복원
git restore --source=HEAD~2 <file>
```
