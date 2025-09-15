---
type: library
related:
  - "[[Libraries/React/Component/Context|Context]]"
---
## 개념

`useContext`는 Context의 현재 값을 반환하는 Hook이다. 이 Hook은 **Context.Provider로 감싸진 컴포넌트 트리 내부**에서 호출돼야 하며, 그렇지 않으면 `createContext`에서 정의한 기본값이 사용된다.

TypeScript 환경에서는 `createContext` 호출 시 **Context의 타입을 명시적으로 지정해야** `useContext`가 타입 추론과 코드 보완에서 제대로 작동한다.

---

## Context 타입 정의와 기본값 처리

TypeScript에서 `createContext`는 `null`을 기본값으로 주는 경우가 많다. 이 경우 `useContext`로 값을 사용할 때에는 **null 체크를 반드시 강제**해야 한다.

```tsx
type CountContextType = {
  count: number;
  setCount: (value: number) => void;
};

const CountContext = createContext<CountContextType | null>(null);
```

---

## Provider 설정

```tsx
export function CountProvider({ children }: { children: React.ReactNode }) {
  const [count, setCount] = useState(0);
  return (
    <CountContext.Provider value={{ count, setCount }}>
      {children}
    </CountContext.Provider>
  );
}
```

Provider는 상태 값을 하위 컴포넌트에게 공급하는 역할을 한다. 이때 `value`는 명시된 타입과 정확히 일치해야 하며, 필요한 모든 값을 포함해야 한다.

---

## useContext 사용

```tsx
function Counter() {
  const context = useContext(CountContext);
  if (!context) throw new Error("CountContext.Provider가 필요합니다");

  const { count, setCount } = context;
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

이처럼 `useContext`는 단순히 값을 읽는 역할만 한다. 상태 변경은 반드시 Context 내부에 포함된 `set` 함수를 통해 이뤄져야 한다.

---

## 커스텀 훅으로 추상화

반복되는 null 체크를 줄이고 재사용성을 높이기 위해 다음과 같이 커스텀 훅을 만들어두는 것이 좋다.

```tsx
function useCount() {
  const context = useContext(CountContext);
  if (!context) throw new Error("CountContext.Provider가 필요합니다");
  return context;
}
```

이제 `useCount()`를 호출하면 항상 타입이 보장된 값이 반환된다.