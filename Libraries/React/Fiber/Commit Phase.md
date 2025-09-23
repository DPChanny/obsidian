---
type: library
parent: "[[Libraries/React/Fiber|Fiber]]"
---
## 개념

**Commit Phase는 Complete Phase에서 준비된 Effect List를 실행하여, 실제 DOM을 변경하고 후처리 작업을 실행하는 단계**다.

이 시점에서만 실제 UI 변화가 발생하며, 이 단계는 **중단되지 않고 반드시 빠르게 완료**되어야 한다.

---

## 작동 방식

1. **Effect List 순회**
    - Complete Phase에서 구성한 Effect List를 순서대로 순회한다.
    - 각 Fiber의 `flags`에 따라 필요한 작업을 실행한다.
2. **DOM 변경 수행**
    - `Placement`: DOM 삽입
    - `Update`: DOM 속성 갱신
    - `Deletion`: DOM 제거
3. **ref 처리**
    - 새롭게 연결되거나 제거되어야 할 `ref`를 처리한다.
4. **effect clean-up 실행 (중요)**
    - 이전 렌더링에서 등록된 effect가 있다면,
        - 그 clean-up 함수가 먼저 실행된다.
        - 이는 Complete 단계에서 등록된 순서 그대로 처리된다.
    - 이로 인해 **의도치 않은 중복 effect, memory leak 등을 방지**할 수 있다.
5. **새 effect 실행**
    - 이후 새롭게 등록된 `useEffect`, `useLayoutEffect` 등을 실행한다.
    - `useLayoutEffect`는 DOM 변경 직후 동기적으로,
    - `useEffect`는 그 이후 비동기적으로 실행된다.

---

## 특징

- **중단 불가능 (non-interruptible)**
    
    → DOM을 부분적으로 바꾸고 멈추면 화면이 깨지기 때문에 반드시 끝까지 실행된다.
    
- **정확한 순서 보장** → clean-up → 새 effect 실행 → hook 안정성 유지
    

---

## 예시 흐름

```
Effect List:
  - Fiber A → Update + clean-up
  - Fiber B → Deletion
  - Fiber C → Placement

Commit Phase:
  ↓
1. Fiber A의 이전 effect clean-up 실행
2. Fiber A의 DOM 갱신
3. Fiber B의 DOM 제거
4. Fiber C의 DOM 삽입
5. Fiber A의 새 useEffect 실행

```

---

## 핵심 요약

- Commit Phase는 **실제 DOM 작업과 후처리 작업이 실행되는 유일한 시점**이다.
- **Effect clean-up은 새 effect 실행 전에 반드시 먼저 실행된다.**
- 이 순서를 보장하기 위해, clean-up은 Complete Phase에서 등록되고, Commit Phase에서 실행된다.