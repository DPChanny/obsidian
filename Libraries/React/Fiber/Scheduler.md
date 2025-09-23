---
type: library
parent: "[[Libraries/React/Fiber|Fiber]]"
---
## 개념

React의 Scheduler는 상태 변경이 발생했을 때, 이를 즉시 처리하지 않고 **언제 처리할지 결정하는 역할**을 한다.

즉, 이 구조는 React가 단순한 렌더링 라이브러리가 아니라, **렌더링 우선순위를 조절하는 프레임워크**로 작동하게 만드는 기반이다.

> 이 구조는 Fiber 트리의 Render Phase 이전에 작동하며, 어떤 작업이 먼저 처리될지를 결정한다.

---

## Lane 모델

React는 우선순위 스케줄링을 위해 **Lane(레인)**이라는 비트마스크 기반 구조를 사용한다.

각 Lane은 특정한 우선순위 레벨을 의미하고, 하나의 업데이트는 특정 Lane에 속한다.

### 주요 Lane 예시:

|Lane 이름|설명|
|---|---|
|`SyncLane`|즉시 실행, 동기 업데이트|
|`InputContinuousLane`|사용자 입력 중 발생한 연속적인 상태 변경|
|`DefaultLane`|일반적인 비동기 렌더링|
|`TransitionLane`|`startTransition`로 감싼 전환 작업|
|`IdleLane`|백그라운드에서 느긋하게 처리해도 되는 작업|

---

## 작동 흐름

### 1. 상태 변경 발생

```tsx
setState(...)
```

→ React는 이 업데이트에 **적절한 Lane을 할당**한다

(ex: 사용자 클릭이면 `SyncLane`, 전환이면 `TransitionLane`)

---

### 2. 업데이트 예약

- 이 업데이트는 **Fiber 루트의 pendingLanes 필드**에 기록된다.
- `ensureRootIsScheduled()` 함수가 실행되며, **Scheduler에 이 작업을 예약**한다.
- Scheduler는 현재 실행 중인 작업과 비교하여,
    - 더 높은 우선순위면 → **선점하고 실행**
    - 더 낮은 우선순위면 → **나중에 실행**

---

### 3. 스케줄링 로직

React 내부의 Scheduler는 다음 기준으로 작업을 제어한다:

- **우선순위**: Lane의 priority가 더 높은 것부터 처리
- **중복 업데이트 병합**: 여러 상태 변경이 같은 Lane이면 함께 처리
- **작업 중단/재개**: 작업이 오래 걸리면 쪼개서 처리

---

## 예시 흐름 요약

```
1. setState → DefaultLane 등록
2. startTransition → TransitionLane 등록
3. Scheduler가 현재 idle 상태면 Transition부터 실행
4. 사용자 클릭 → SyncLane → 바로 실행 (Transition 작업 중단)
5. 클릭 처리 완료 → Transition 이어서 실행
```

---

## 결론

React의 Scheduler는 Fiber 렌더링 이전에 **작업의 우선순위를 결정하고, 실행 타이밍을 조정**하는 핵심 컴포넌트다.

Lane 기반 스케줄링을 통해 React는:

- 사용자 인터페이스의 **응답성 유지**
- 복잡한 화면에서도 **느낌 없는 전환**
- 다중 작업 충돌에서 **중요한 것부터 우선 처리**

할 수밖에 없는 구조로 진화했다.