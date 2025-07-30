---
type: library
parent: "[[Libraries/React/Component/Hook|Hook]]"
related:
  - "[[Libraries/React/Component/Lifecycle|Lifecycle]]"
---
## 개념

`useEffect`는 함수형 컴포넌트가 렌더링된 후 특정 작업을 수행하도록 하는 Hook이다.

React 컴포넌트는 렌더링될 때마다 함수처럼 다시 실행되므로, 외부 작업의 실행 시점을 명확히 제어해야 한다. `useEffect`는 컴포넌트가 **마운트된 직후**, **업데이트 직후**, 또는 **언마운트 직전에 실행할 작업**을 지정할 수 있다.

이러한 작업들은 주로 외부 API 호출, 이벤트 등록, 타이머 설정, DOM 조작과 같이 React의 렌더링 흐름과 무관한 부수 효과(side effect)다.

---

## 기본 사용법

```tsx
import { useEffect } from "react";

function App() {
  useEffect(() => {
    console.log("렌더링 이후 실행됨");
  });

  return <div>Hello</div>;
}
```

이 코드는 **컴포넌트가 렌더링될 때마다 실행**된다. 즉, 상태나 props가 변경될 때도 다시 실행된다.

---

## 의존성 배열로 실행 시점 제어

### 1. 마운트 시 한 번만 실행 (componentDidMount 대응)

```tsx
useEffect(() => {
  console.log("처음 마운트될 때 한 번 실행");
}, []);
```

빈 배열을 전달하면, 컴포넌트가 처음 렌더링된 직후 한 번만 실행된다.

---

### 2. 특정 값이 바뀔 때만 실행 (componentDidUpdate 대응)

```tsx
useEffect(() => {
  console.log("count가 바뀔 때마다 실행");
}, [count]);
```

배열에 `count`를 넣으면, `count` 값이 변경될 때만 effect가 실행된다. **이전 값과 비교하여 실제로 변경되었을 때만 실행된다.**

---

### 3. 매번 실행 (렌더링마다 실행)

```tsx
useEffect(() => {
  console.log("모든 렌더링마다 실행");
});
```

의존성 배열을 생략하면, 컴포넌트가 렌더링될 때마다 실행된다. 이는 권장되지 않으며, 성능에 영향을 주거나 반복 실행이 중요한 작업에는 피해야 한다.

---

## 클린업 함수

Effect는 선택적으로 **정리(clean-up)** 함수를 반환할 수 있다. 이 함수는 다음 두 경우에 실행된다:

- 컴포넌트가 언마운트될 때
- 다음 effect가 실행되기 직전 (의존성이 바뀌었을 때)

```tsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log("running...");
  }, 1000);

  return () => {
    clearInterval(timer);
    console.log("정리됨");
  };
}, []);
```

이 구조는 `componentWillUnmount`와 동일한 효과를 제공한다.

---

## 주의할 점

- `useEffect` 내부에서 상태를 변경하면 **렌더링 → effect 실행 → 상태 변경 → 또 렌더링** 순환이 발생할 수 있다. 의존성 배열을 정확히 설정하지 않으면 무한 루프가 발생할 수 있다.
- 상태를 외부 함수나 로직에서 가져오지 말고, 반드시 의존성 배열에 명시해야 한다. 그렇지 않으면 최신 값을 보장받을 수 없다.
- **비동기 함수 자체를 `useEffect` 콜백으로 사용할 수 없다.** 대신 내부에서 async 함수를 선언하고 호출해야 한다.

```tsx
useEffect(() => {
  async function fetchData() {
    const res = await fetch(...);
    // ...
  }
  fetchData();
}, []);
```