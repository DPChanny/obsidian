---
---
## 개념

Props는 React 컴포넌트가 **외부로부터 전달받는 값**이다.

함수의 매개변수처럼 컴포넌트에 값을 전달하며, **읽기 전용**이다.

컴포넌트가 독립된 UI 단위라고 했을 때, Props는 **이 단위가 외부와 소통하는 유일한 채널**이다.

내부 상태(`state`)는 바뀔 수 있지만, Props는 **부모 컴포넌트가 내려주는 값이므로 자식은 수정할 수 없다.**

TypeScript에서는 Props의 구조를 명시적으로 정의할 수 있다.

이로 인해 컴포넌트 사용 시 타입 안정성과 자동 완성의 이점을 누릴 수 있다.

Props는 일반적으로 `type` 또는 `interface`를 통해 정의하며, 컴포넌트 함수의 인자로 구조 분해하여 받는다.

```tsx
type GreetingProps = {
  name: string;
};

const Greeting = ({ name }: GreetingProps): JSX.Element => {
  return <p>안녕하세요, {name}님!</p>;
};

```

또는 `React.FC`를 사용할 수도 있다. `React.FC`는 `props` 타입과 함께 `JSX.Element` 반환 타입을 포함하며,

**암묵적으로 `children` 속성을 포함**한다는 특징이 있다.

```tsx
const Greeting: React.FC<GreetingProps> = ({ name, children }) => {
  return <div>
    <p>안녕하세요, {name}님!</p>
    {children}
  </div>;
};

```

하지만 `children`을 명시적으로 제어하고 싶을 때는 `React.FC` 대신 직접 시그니처를 선언하는 방식이 더 유연하다.

---

## 목적

Props의 핵심 목적은 다음과 같다:

- **컴포넌트 재사용성 확보**: 내부 로직은 그대로 두고, 외부 데이터만 바꿔 다양한 결과를 낼 수 있다
- **상위 → 하위 단방향 데이터 흐름 유지**: 상위 컴포넌트에서 데이터를 통제하고, 하위 컴포넌트는 표현에 집중한다
- **컴포넌트 트리 구성**: 각 컴포넌트는 Props를 통해 트리 상에서 부모-자식 관계로 데이터를 연결한다

---

## 예시

### 1. 단순한 값 전달

```tsx
const Title = ({ text }: { text: string }) => {
  return <h1>{text}</h1>;
};

<Title text="React 개념 정리" />

```

---

### 2. 함수 전달

```tsx
const Clicker = ({ onClick }: { onClick: () => void }) => {
  return <button onClick={onClick}>클릭</button>;
};

<Clicker onClick={() => console.log("눌림")} />

```

---

### 3. 컴포넌트 중첩 (children 사용)

```tsx
const Card = ({ children }: { children: React.ReactNode }) => {
  return <div className="card">{children}</div>;
};

<Card>
  <p>안에 들어간 내용</p>
</Card>

```

---

## 왜 읽기 전용인가?

Props가 읽기 전용이라는 점은 React의 **단방향 데이터 흐름(one-way data flow)** 철학과 맞닿아 있다.

만약 자식이 받은 props를 수정할 수 있다면, 어떤 값이 어디서 바뀌었는지 추적할 수 없고

결과적으로 애플리케이션의 상태 일관성을 잃게 된다.

React는 이 문제를 방지하기 위해 props를 항상 **불변(immutable)**으로 다루게 강제하고 있다.