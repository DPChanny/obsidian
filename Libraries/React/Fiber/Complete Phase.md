---
type: library
parent: "[[Libraries/React/Fiber|Fiber]]"
---
## 개념

**Complete Phase는 Render Phase에서 계산된 Fiber 트리를 정리하는 단계**다.

React는 이 시점에 어떤 DOM 조작이 필요한지를 판단하고, 이를 나중에 한꺼번에 실행할 수 있도록 **Effect List**로 정리해 둔다.

> 또한 이전 렌더링에서 등록된 effect의 clean-up 함수도 이 시점에 Effect List에 등록된다.

---

## 작동 방식

1. **Fiber 트리 후방 순회 (부모 → 자식)**
    
    Render Phase에서는 자식부터 올라가는 DFS였지만, 여기서는 부모에서 자식으로 내려가며 각 Fiber의 작업을 정리한다.
    
2. **Effect Tag 확인**
    
    - 각 Fiber의 `flags`를 기반으로 DOM 변경이 필요한지를 판단한다:
        - `Placement`: 삽입
        - `Update`: 속성 갱신
        - `Deletion`: 제거
3. **Effect List 생성**
    
    - 변경이 필요한 Fiber만 골라 **연결 리스트 형태로 Effect List 구성**
    - 최종적으로 `root Fiber`가 이 리스트의 시작점을 가리키도록 설정된다.
4. **clean-up 함수 등록 (중요)**
    
    - 만약 current Fiber에 `useEffect` 또는 `useLayoutEffect`가 있었고, workInProgress에서 다시 등록되었다면,
    - React는 **이전 effect의 clean-up 함수를 Effect List에 먼저 등록**한다.
    - 이로 인해 Commit Phase에서 먼저 정리(clean-up)되고, 새 effect가 실행된다.

---

## 중요한 특징

- DOM은 여전히 변경되지 않는다
- **실행 계획만 정리하는 단계**다
- clean-up도 이 시점에 같이 리스트에 포함되므로 **Effect 순서가 정확하게 보장된다**

---

## 예시 흐름

```
Render Phase → workInProgress 트리 구성
  ↓
Complete Phase 시작
  ↓
각 Fiber의 flags 확인 → Effect List에 등록
  ↓
current Fiber의 이전 effect → clean-up 함수 등록
  ↓
Effect List 완성 → Commit Phase로 전달

```

---

## 핵심 요약

- Complete Phase는 DOM에 적용할 변경 사항을 모아 **Effect List**로 정리한다.
- 이전 렌더링에서 등록된 effect의 **clean-up 함수도 여기서 같이 등록**된다.
- 이로 인해 React는 다음 Commit 단계에서 정확히 정리 후 새 effect를 실행할 수밖에 없다.