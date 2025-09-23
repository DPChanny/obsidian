---
type: library
parent: "[[Libraries/React/Component/Hook|Hook]]"
related:
  - "[[Libraries/React/Component/Ref|Ref]]"
---
`useRef`는 React 함수형 컴포넌트 내부에서 **값을 저장하고 유지하기 위한 훅**이다.

렌더링 사이에서도 사라지지 않는 **mutable한 객체 컨테이너**를 제공하며, 이 객체는 `.current`라는 속성을 가진다.

---

## 개념

`useRef`는 두 가지 목적으로 사용된다:

1. **DOM 참조 연결 (ref forwarding)** → `ref`를 DOM 요소에 연결해 직접 접근할 수 있도록 함
2. **렌더링에 관여하지 않는 값 저장소** → 상태처럼 값을 유지하되, 값 변경이 리렌더링을 유발하지 않음

```tsx
const ref = useRef<T>(초기값);

```

이렇게 호출하면 `{ current: 초기값 }` 구조의 객체가 생성되고, 이 객체는 컴포넌트가 리렌더링돼도 **동일한 참조값으로 유지**된다.

---

## DOM에 ref 연결

```tsx
import { useRef, useEffect } from "react";

function FocusInput() {
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    if (inputRef.current) {
      inputRef.current.focus();
    }
  }, []);

  return <input ref={inputRef} />;
}

```

- `inputRef.current`에는 실제 DOM 노드가 들어가고
- 이 노드는 마운트 이후 `useEffect` 타이밍에만 존재한다

---

## 상태 대용: 렌더링 없이 값 저장

```tsx
function Timer() {
  const countRef = useRef(0);

  useEffect(() => {
    const id = setInterval(() => {
      countRef.current += 1;
      console.log("현재 값:", countRef.current);
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return <div>콘솔을 확인하세요</div>;
}

```

이 방식은 `useState`처럼 값이 계속 쌓이지만, **렌더링을 트리거하지 않기 때문에**

**부수 효과만 있고 UI 반영이 필요 없는 경우에 최적**이다.

---

## 특징 정리

- `.current` 필드를 가진 객체를 반환한다
- 초기값은 첫 렌더링 시 한 번만 적용되며 이후 유지된다
- 값이 바뀌어도 리렌더링을 유발하지 않는다
- DOM 노드 접근 외에도, 어떤 값이든 유지할 수 있는 **mutable한 기억장치**로 쓰인다

---

## 왜 state 대신 쓰는가?

- 상태를 쓸 경우, 값이 바뀔 때마다 컴포넌트가 리렌더링된다
- 그러나 UI에는 영향을 주지 않고, **내부적으로 값만 누적하거나 기록**하고 싶은 경우 `useRef`가 적절하다

이런 경우가 대표적이다:

- setInterval 안에서 현재 값 누적
- 외부 이벤트 핸들러 내부에서 최신 값 참조
- 컴포넌트 상태 변화와 무관한 flag 저장

---

## 결론

`useRef`는 **"DOM 참조 + 렌더링 비회전 값 저장"** 이 두 역할 모두를 맡을 수 있다.

그러나 어떤 값을 `.current`에 넣는다고 해서 UI가 다시 그려지는 건 아니며,

**state와는 엄격히 다른 동작 방식**을 가진다.

→ 그래서 실제 동작 목적에 따라 `useState`, `useRef`, `useReducer` 중 적절한 도구를 선택할 수밖에 없다.