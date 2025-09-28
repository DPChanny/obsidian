---
type: library
related:
  - "[[Libraries/React/Component/State|State]]"
---
## 개념

`useState`는 React에서 상태를 함수형 컴포넌트 내부에서 선언할 수 있게 해주는 Hook이다.

함수형 컴포넌트는 렌더링마다 처음부터 다시 실행되기 때문에 일반 변수는 값을 기억하지 못한다. `useState`는 React 내부의 상태 저장소와 연결된 값을 반환하며, 그 값이 바뀔 때 컴포넌트를 다시 실행하도록 만든다.

이렇게 React는 상태를 컴포넌트 외부에서 따로 관리하면서, UI는 오직 현재 상태에 따라 그려지게 된다. 이 구조 덕분에 UI의 선언성과 예측 가능성이 확보된다.

---

## 목적

- 컴포넌트의 상태를 값으로 저장하고, 상태 변화에 따라 UI를 자동으로 갱신
- 함수형 컴포넌트의 구조를 유지하면서도 상태 기반 인터랙션을 구현
- 외부에서 전달받은 props가 아닌, 컴포넌트 내부에서 관리할 수 있는 데이터 제공
- React의 리렌더링 흐름에 맞춰 반응형 UI를 구성

---

```tsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    &lt;button onClick={() =&gt; setCount(count + 1)}&gt;
      Clicked {count} times
    &lt;/button&gt;
  );
}
```

```
(실행 결과)
Clicked 0 times
Clicked 1 times
Clicked 2 times
...
```

---

## 작동 방식

1. `useState(초기값)`을 호출하면 React는 내부 상태 저장소에 값을 등록한다.
2. 반환된 배열의 첫 번째 요소는 현재 값, 두 번째 요소는 상태를 바꾸는 함수다.
3. `setState`를 호출하면 값이 갱신되고, 컴포넌트는 다시 실행된다.
4. 리렌더링된 컴포넌트에서도 React는 이전에 저장해 둔 상태값을 다시 꺼내서 전달한다.

즉, 컴포넌트는 매번 새로 실행되지만, 상태는 React가 따로 관리하고 유지한다.

---

## 특징 정리

- 상태를 바꾸는 유일한 방법은 `setState`를 호출하는 것
- 상태 변경은 자동으로 리렌더링을 유발
- 여러 상태값을 선언하려면 `useState`를 여러 번 사용하면 된다
- 이전 상태에 기반한 변경은 콜백 함수 방식으로 처리

```tsx
setCount(prev => prev + 1);
```

- 상태 변경은 비동기처럼 작동하므로, 변경 직후 값은 아직 반영되지 않는다

---

## 변수 이름은 자유롭게 정할 수 있다

```tsx
const [count, setCount] = useState(0);
```

여기서 `count`와 `setCount`는 단지 배열 구조 분해로 꺼낸 이름일 뿐이다. 아래처럼 이름을 다르게 정해도 완전히 동일하게 작동한다.

```tsx
const [value, updateValue] = useState(0);
```

중요한 건 구조이지, 이름은 개발자가 자유롭게 정할 수 있다.

---

## 상태값을 직접 바꾸면 아무 일도 일어나지 않는다

```tsx
let [count, setCount] = useState(0);

count = count + 1;
```

이렇게 직접 값을 바꾸면 React는 변경 사실을 알지 못한다. `useState`는 내부 상태 저장소에 상태값을 저장하고 있으며, `setCount`를 통해서만 그 값이 갱신됐다는 사실을 감지한다.

UI를 갱신하려면 반드시 `setCount(...)`를 사용해야 한다.

```tsx
setCount(count + 1);
```

---

## 요약

- 상태는 값 자체가 아니라, React 내부에 저장된 **위치 기반 참조**
- 상태를 바꾸려면 React에게 "바뀌었다"고 알려야 하므로 `setState`를 꼭 호출해야 한다