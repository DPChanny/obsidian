---
type: library
---
React는 선언형 UI를 지향한다. 대부분의 경우 명시적인 DOM 접근 없이도 상태 기반으로 UI를 구성할 수 있다. 그러나 다음과 같은 경우에서는 **직접 DOM 또는 인스턴스에 접근해야 할 필요**가 있다:

- 특정 input 요소에 focus를 주고 싶을 때
- 외부 라이브러리를 통해 DOM 노드를 제어해야 할 때
- 크기, 위치 등 layout 정보를 직접 측정할 때
- imperative 방식의 메서드 호출이 필요한 컴포넌트 제어

이런 상황에서 사용하는 수단이 바로 `ref`다.

---

## JSX 상의 예시

```jsx
<MyComponent ref={someRef} />
```

이 구문은 `ref`라는 속성이 일반 props처럼 작성되어 있지만, 실제로는 `React.createElement` 내부에서 다음처럼 처리된다:

```
React.createElement(MyComponent, { ref: someRef });
```

그러나 **React는 내부적으로 `ref`를 `props`와는 완전히 분리해서 처리**한다. 컴포넌트 안에서 `props.ref`로 접근해도 값이 없다. 대신 React가 이 `ref` 값을 기억하고, 렌더링 이후에 연결을 시도한다.

---

## 내부 동작

```jsx
function MyComponent(props) {
  console.log(props.ref); // undefined
  ...
}
```

React는 createElement 시점에 전달된 `ref` 값을 `props`에 포함시키지 않고, 별도로 `ReactElement.ref` 필드에 담는다:

```
{
  type: MyComponent,
  props: { ... },
  key: null,
  ref: someRef
}
```

이후 렌더링이 완료되면, React는 이 `ref` 객체의 `.current`에 실제 DOM 노드 또는 클래스 인스턴스를 할당한다.

---

## ref가 필요한 이유

`ref`는 **React 생명주기 바깥에서 외부 시스템을 건드리기 위한 유일한 수단**이다. 상태와 props는 React의 제어 흐름 안에 있지만, `ref`는 그 바깥에서 DOM이나 컴포넌트를 직접 조작하는 "탈 React적인 접근"이다. 따라서 남용하면 안 되며, **불가피한 경우에만 사용해야 한다.**

---

## 특징 정리

- `ref`는 JSX에서 props처럼 보이지만 props가 아니다
- 내부적으로 ReactElement 객체의 별도 필드로 분리된다
- 마운트 이후 연결된다 (`useEffect` 타이밍에서 접근 가능)
- DOM 노드 또는 클래스 컴포넌트 인스턴스를 참조할 수 있다
- 함수형 컴포넌트에는 직접 연결되지 않으며, 이를 위해선 `forwardRef`가 필요하다