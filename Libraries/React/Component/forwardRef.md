---
type: library
related:
  - "[[Libraries/React/Component/Ref|Ref]]"
---
# forwardRef

`forwardRef`는 React에서 **부모가 만든 `ref`를 자식 컴포넌트의 내부 DOM 요소나 메서드에 연결할 수 있도록** 해주는 고차 함수이다.

기본적으로 함수형 컴포넌트는 `ref`를 props처럼 받을 수 없다.

React는 `ref`를 **별도로 분리된 예약된 필드**로 처리하기 때문이다.

이를 우회해 명시적으로 전달하고 연결할 수 있는 유일한 수단이 `forwardRef`다.

---

## 기본 형태

```tsx
const Input = forwardRef<HTMLInputElement, { label: string }>(
  ({ label }, ref) => {
    return (
      <label>
        {label}
        <input ref={ref} />
      </label>
    );
  }
);

// 사용
const ref = useRef<HTMLInputElement>(null);
<Input ref={ref} label="이름" />

```

- `ref`는 React가 자동으로 주입해주는 두 번째 인자이며
- 이 `ref`를 내부 `<input>` 요소에 연결하면
- 부모에서 `ref.current`로 해당 DOM 요소에 직접 접근할 수 있다

---

## forwardRef는 고차 함수다

React 내부에서 `forwardRef`는 아래와 같이 작동한다:

```tsx
function forwardRef(renderFn) {
  return function ForwardedComponent(props) {
    return renderFn(props, ref);
  };
}

```

즉, **함수를 인자로 받아 새로운 컴포넌트를 반환하는 함수**,

즉 **고차 함수(Higher-Order Function)**다.

이 구조 덕분에 외부에서 ref를 넘기고, 내부에서 원하는 DOM 요소나 객체에 연결할 수 있다.

---

## 언제 forwardRef를 써야 하는가?

컴포넌트를 설계할 때 다음 질문을 던져야 한다:

> "부모 컴포넌트가 자식 내부의 DOM 요소나 메서드에 직접 접근해야 하는가?"

예를 들어:

- `<input>`에 `.focus()`를 주고 싶다
- 자식 컴포넌트의 `reset()`, `scroll()` 같은 메서드를 부모가 호출하고 싶다

이런 시나리오에서는 자식이 `forwardRef`로 선언되어 있어야만,

부모의 `ref`가 내부 DOM이나 메서드에 연결될 수 있다.

→ 이 요구가 생기는 순간부터 **컴포넌트는 반드시 forwardRef 기반으로 설계돼야 할 수밖에 없다.**

---

## 사용 예시

### 1. DOM에 직접 접근: focus 주기

```tsx
const FocusableInput = forwardRef<HTMLInputElement, { label: string }>(
  ({ label }, ref) => {
    return (
      <label>
        {label}
        <input ref={ref} />
      </label>
    );
  }
);

const App = () => {
  const inputRef = useRef<HTMLInputElement>(null);

  return (
    <>
      <button onClick={() => inputRef.current?.focus()}>포커스</button>
      <FocusableInput ref={inputRef} label="이름" />
    </>
  );
};

```

---

### 2. 내부 메서드 노출: reset 기능 제공

```tsx
type FormHandle = {
  reset: () => void;
};

const MyForm = forwardRef<FormHandle, {}>((_, ref) => {
  const [value, setValue] = useState("");

  useImperativeHandle(ref, () => ({
    reset: () => setValue("")
  }));

  return (
    <input
      value={value}
      onChange={e => setValue(e.target.value)}
      placeholder="입력해보세요"
    />
  );
});

const App = () => {
  const formRef = useRef<FormHandle>(null);

  return (
    <>
      <button onClick={() => formRef.current?.reset()}>초기화</button>
      <MyForm ref={formRef} />
    </>
  );
};

```

- 이 구조는 단순히 DOM에 접근하는 걸 넘어,
- 자식이 부모에게 **명령 가능한 메서드 인터페이스를 제공하는 구조**
- 완전히 함수형이지만, Imperative한 명령을 외부에 개방

---

## 정리

- `forwardRef`는 함수형 컴포넌트가 외부 `ref`를 받을 수 있게 해주는 유일한 공식 API다
- 내부적으로는 `ref`를 두 번째 인자로 전달하고, 그 값을 DOM이나 객체에 연결하는 구조
- 이 때문에 React에서는 **"ref를 받아야 하는 컴포넌트는 처음부터 forwardRef로 설계돼야 한다"**는 패턴이 자연스럽게 형성된다
- UI만 담당하는 컴포넌트는 일반 함수형으로, **외부 제어가 필요한 컴포넌트는 forwardRef 구조로 명시적 설계**를 해야 한다