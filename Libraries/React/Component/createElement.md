---
related:
  - "[[Libraries/React/JSX|JSX]]"
---
React.createElement는 JSX가 변환되어 도달하는 실질적인 함수 호출 형태이며, 모든 React 컴포넌트와 엘리먼트의 기초가 되는 구조다. 이 함수는 Virtual DOM의 단위인 **ReactElement 객체**를 생성하는 역할을 한다.

---

## 기본 구조

```tsx
React.createElement(type, config, ...children)
```

- `type`: 문자열이면 DOM 태그, 함수면 React 컴포넌트
- `config`: props 역할을 하는 객체, 여기서 `key`와 `ref`는 별도로 분리됨
- `children`: JSX의 자식 노드들

---

## 내부 동작

React.createElement는 다음과 같은 방식으로 동작한다:

```tsx
function createElement(type, config, ...children) {
  let props = {};
  let key = null;
  let ref = null;

  if (config != null) {
    if ('key' in config) key = config.key;
    if ('ref' in config) ref = config.ref;

    for (let prop in config) {
      if (prop !== 'key' && prop !== 'ref') {
        props[prop] = config[prop];
      }
    }
  }

  if (children.length === 1) {
    props.children = children[0];
  } else if (children.length > 1) {
    props.children = children;
  }

  return {
    $$typeof: Symbol.for('react.element'),
    type,
    key,
    ref,
    props,
    _owner: null
  };
}
```

---

## key와 ref의 분리 이유

- `key`: 리스트 렌더링에서 React가 엘리먼트를 식별할 수 있게 함
- `ref`: DOM 혹은 컴포넌트 인스턴스에 대한 접근 수단 제공

이 둘은 컴포넌트 내부에서 `props.key`나 `props.ref`로 접근할 수 없다. 대신 React 내부에서 별도로 참조된다.

---

## 결과적으로 생성되는 ReactElement

```tsx
{
  $$typeof: Symbol(react.element),
  type: 'div' | MyComponent,
  key: 'some-key',
  ref: someRef,
  props: {
    className: "box",
    children: "내용"
  },
  _owner: null
}
```

이 객체는 Virtual DOM의 구성 요소로써, 이후 React가 실제 DOM과 비교(diffing)하여 최적의 방식으로 반영한다.

---

### 정리

JSX는 `React.createElement` 호출로 변환되며, 이 함수는 **type + props + children + key/ref**를 조합한 객체를 반환한다. `key`와 `ref`는 단순한 props가 아니라 React 내부에서 엘리먼트 추적과 참조 관리에 쓰이기 때문에 **의도적으로 props에서 분리되어 처리된다**. 이는 컴포넌트 설계 시 반드시 고려해야 할 중요한 사항이다.