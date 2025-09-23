---
type: library
parent: "[[Libraries/React/Component|Component]]"
---
## 개념

React에서 컴포넌트는 상태나 props가 바뀔 때마다 다시 실행되며, 그 과정에서 생명주기(lifecycle)를 가진다.

라이프사이클이란 컴포넌트가 생성되고, 변화하고, 사라지는 일련의 흐름을 말한다. React는 이 흐름 속에서 특정 타이밍에 원하는 작업을 실행할 수 있도록 Hook을 제공한다.

함수형 컴포넌트에서는 더 이상 `componentDidMount`, `componentDidUpdate`, `componentWillUnmount` 같은 메서드는 존재하지 않지만, 그 모든 역할을 **`useEffect` 하나로 통합**해서 수행하게 되었다.

---

## 목적

- 특정 타이밍에 부수 효과(side effect)를 실행하고 정리
- 외부 API 호출, 구독 설정, 타이머 등록 등 DOM 외부 작업 실행
- 리렌더링 시점마다 조건에 따라 작업 수행
- 컴포넌트 언마운트 시 정리(clean-up) 작업 수행

---

## 왜 이렇게 작동하는가?

함수형 컴포넌트는 선언형 UI를 지향한다. 즉, UI를 상태의 함수처럼 단순하게 정의하고, 상태가 바뀌면 컴포넌트를 다시 실행해서 UI를 갱신한다.

이 구조에서는 **라이프사이클 타이밍을 따로 코드로 쪼개기보단**, "렌더링 후 어떤 작업을 수행해야 하는가?"를 선언하는 게 더 자연스럽다.

그래서 React는 `useEffect`를 통해 "렌더링 이후 실행할 함수"를 등록하게 하고, 그 안에서 mount, update, unmount 시점을 모두 처리하게 만들었다.