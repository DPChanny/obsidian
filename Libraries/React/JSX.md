---
---
JSX는 JavaScript 안에서 XML-like 문법을 사용할 수 있게 해주는 React의 확장 문법이다. 겉보기에는 HTML과 유사하지만, 실제로는 JavaScript로 변환되는 코드다. JSX는 React의 렌더링 구조와 결합되어, 상태 기반 UI를 선언적으로 표현할 수 있게 한다.

### 왜 JSX가 필요한가?

JSX는 두 가지 이유로 React에서 채택되었다:

1. **상태 표현의 직관성**
    - 상태가 바뀌면 컴포넌트 함수가 다시 실행되고, 새로운 JSX가 반환된다.
    - JSX는 JavaScript 표현식과 상태 값을 직접 포함할 수 있어, 상태 기반 화면의 변화를 직관적으로 작성할 수 있다.
2. **UI 구조의 가독성**
    - React는 UI를 함수처럼 선언적으로 기술하고자 했고, JSX는 그 목적에 부합하는 문법이다.
    - HTML처럼 보이기 때문에 시각적으로 구조 파악이 빠르고, 실제로는 JavaScript의 일부로 동작한다.

### 내부적으로 어떤 역할을 하는가?

JSX는 본질적으로 JavaScript 코드 안에 문자열 기반 UI 구조를 선언적으로 삽입하기 위한 "형식 있는 문자열 출력 방식"이다. 일종의 print-formatted string처럼, 변수나 상태를 중괄호로 포함하고 최종적으로 하나의 HTML-like 구조를 반환한다.

실제로 JSX는 브라우저가 직접 이해할 수 있는 문법이 아니며, Babel과 같은 트랜스파일러에 의해 `React.createElement(...)` 형태의 JavaScript 함수 호출로 변환된다. 이 변환 결과는 Virtual DOM 구조를 만들고, 이후 실제 DOM으로 반영된다. 즉, JSX는 **React가 JavaScript 내부에서 HTML을 생성하고 구성하는 특별한 방식**이라고 볼 수 있다.

### JSX와 상태 기반 렌더링의 관계

JSX는 상태를 직접 추적하거나 관리하지 않는다. JSX는 단순히 **현재 실행 시점의 상태 값을 표현식으로 포함한 UI 구조를 생성하는 역할**을 할 뿐이다. 상태가 변경되면 React가 해당 컴포넌트를 다시 실행하고, 이때 새로운 상태를 기반으로 다시 JSX가 생성된다.

따라서 JSX는 상태 변화의 흐름에 반응하는 것이 아니라, **React가 다시 실행될 때 상태의 최신 값을 기반으로 출력되는 일회성 결과물**이다. React가 이를 감지하고 UI를 업데이트할 수 있는 이유는, JSX 그 자체가 아니라 `setState` 계열 함수 호출을 통해 리렌더링이 예약되기 때문이다.

## JSX 상의 속성과 실제 동작의 차이

JSX에서 다음과 같이 작성하면:

```tsx
<MyComponent
  key="card-1"
  ref={myRef}
  title="Hello"
  user={{ name: "Jane", age: 28 }}
  onClick={() => alert("clicked")}
>
  <Header />
  <Content />
  <Footer />
</MyComponent>
```

겉보기에는 모든 속성이 동일하게 `props`처럼 보인다. 그러나 실제로는 다음과 같은 형태의 `React.createElement` 호출로 변환된다:

```tsx
React.createElement(
  MyComponent,
  {
    title: "Hello",
    user: { name: "Jane", age: 28 },
    onClick: () => alert("clicked"),
    key: "card-1",
    ref: myRef
  },
  React.createElement(Header, null),
  React.createElement(Content, null),
  React.createElement(Footer, null)
)

```

```tsx
React.createElement(
  MyComponent,
  {
    title: "Hello",
    user: { name: "Jane", age: 28 },
    onClick: () => alert("clicked"),
    children: [
      React.createElement(Header, null),
      React.createElement(Content, null),
      React.createElement(Footer, null)
    ],
    key: "card-1",
    ref: myRef
  }
)
```

이 코드만 보면 `key`, `ref`, `children`도 모두 `config` 객체에 포함되어 있으므로 마치 **props의 일부처럼 보일 수 있다.** 특히 오른쪽 예시처럼 `children`까지 명시적으로 props 내부에 들어 있는 구조는 **표현상으로 직관적**이기 때문에 흔히 실제 동작도 저렇게 이해되곤 한다.

하지만 **React는 실제로 왼쪽 예시와 같은 방식으로 처리**한다. 즉, `children`은 `config`의 속성이 아니라 `React.createElement` 함수의 세 번째 이후 인자로 전달되고, 내부에서 `props.children`으로 병합된다. 반면, `key`와 `ref`는 `config` 안에 포함되어 전달되지만, **React 내부에서는 이들을 props에서 분리하여 별도로 저장한다.**

- `key`는 리스트 렌더링에서 React가 엘리먼트를 식별하는 데 사용되며,
- `ref`는 DOM 혹은 컴포넌트 인스턴스에 직접 접근하기 위한 수단이다.

이들은 컴포넌트 내부의 `props`에서는 접근할 수 없고, **React가 컴포넌트 외부에서 추적하고 관리하는 메타 정보로 취급된다.**

따라서 JSX는 모든 속성을 동일한 문법으로 기술할 수 있게 해주지만, **React의 내부 동작은 `key`, `ref`, `children`을 일반 props와 다르게 처리할 수밖에 없는 구조**로 되어 있다. 이 차이점을 이해하는 것이 JSX와 React의 실제 작동 방식을 올바르게 파악하는 첫걸음이다. 더 구체적인 내부 로직은 `React.createElement` 문서에서 설명된다.