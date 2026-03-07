---
---
## 개념

**Render Phase는 React가 새 UI를 계산하는 단계**다.

Fiber 구조는 이 단계에서 각 컴포넌트를 **Fiber Node라는 단위로 쪼개고**, 이를 하나씩 처리하면서 어떤 UI를 그려야 할지를 결정한다.

이때 사용되는 핵심 기술이 **Double Buffering 구조**다. 이 구조 덕분에 React는 렌더링을 중단할 수 있고, 안전하게 다음 프레임으로 이어가거나 사용자 입력을 우선 처리할 수 있게 된다.

---

## Double Buffering 구조

### 기본 아이디어

Fiber는 **각 컴포넌트마다 두 개의 버퍼를 유지**한다:

- `current`: 현재 화면에 실제 반영된 Fiber
- `workInProgress`: 이번 업데이트를 위해 새로 계산 중인 Fiber

이 둘은 `alternate` 포인터로 서로 연결되어 있으며, 다음과 같은 형태다:

```
[A 컴포넌트]
current        ←→       workInProgress
  (현재 상태)              (새 상태 계산 중)

```

> 이 구조는 마치 캔버스 두 장을 번갈아 쓰듯, 하나는 사용자에게 보여주고 하나는 새로 그리는 역할을 한다.

---

## 작동 방식

1. 상태 변경이 감지되면, `current` Fiber를 복사해 `workInProgress` Fiber를 생성한다.
2. 이 `workInProgress`를 기반으로 새로운 상태와 UI 구조를 계산한다.
3. 계산이 끝나면, `workInProgress`가 `current`로 바뀌고 화면에 반영된다.

---

## 순회 방식

- Render Phase는 **자식부터 시작하는 DFS(Depth-First Search)** 방식으로 Fiber 트리를 순회한다.
- 각 컴포넌트를 실행하고, 그 결과로 자식 요소들을 다시 Fiber로 구성한다.
- 이 순회 중에는 컴포넌트가 실제로 "렌더링된다"고 느낄 수 있지만, 이 시점까지는 **DOM에는 아무 일도 일어나지 않는다.**

---

## 중단 가능한 이유

- React는 이 트리 순회를 작업 단위로 나누고, 각 단위를 처리할 때 **시간이 너무 오래 걸리면 중단**한다.
- 이를 가능하게 한 것이 Double Buffering이다. 계산이 `current`가 아니라 `workInProgress`에서 이뤄지므로, 중단되더라도 화면이 깨지지 않는다.

---

## 예시 흐름

```
setState() 호출
  ↓
current Fiber 트리 복사 → workInProgress 트리 생성
  ↓
Render Phase 시작 (자식부터 순회하며 컴포넌트 실행)
  ↓
필요 시 중단 (다음 프레임에서 이어서 처리)
  ↓
모든 노드 계산 완료 → Complete Phase로 이동

```

---

## 핵심 요약

- Render Phase는 UI의 **새로운 상태를 계산**하는 단계
- **Double Buffering 구조** 덕분에 기존 화면을 건드리지 않고 계산 가능
- 중단 가능한 렌더링이 가능해지고, React의 **응답성과 확장성의 기반**이 된다