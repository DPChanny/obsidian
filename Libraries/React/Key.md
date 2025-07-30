---
type: library
parent: "[[Libraries/React|React]]"
---
## 개념

React는 리스트 렌더링 시 각 항목의 고유성을 판단하기 위해 `key`를 사용한다. 이 `key`는 단순한 식별자 역할을 넘어서, React 내부에서 **컴포넌트 인스턴스를 식별하고 상태를 유지할지 초기화할지를 결정하는 기준**으로 작동한다.

---

## 왜 key가 중요한가?

리스트 렌더링에서는 컴포넌트가 자주 추가되거나 순서가 바뀌게 되므로, 각 항목이 무엇인지 정확히 식별할 수 있어야 한다. 이때 `key`가 없거나 index와 같이 불안정한 값을 key로 사용하면, React는 **컴포넌트를 잘못 매칭하고**, 그 결과 **상태도 잘못된 컴포넌트에 이어지게 된다.**

---

## 예제: 상태를 가지는 하위 컴포넌트에서 key를 index로 사용한 경우

```jsx
function Item({ label }) {
  const [text, setText] = useState('');

  return (
    <div>
      <span>{label}</span>
      <input
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
    </div>
  );
}

function App() {
  const [items, setItems] = useState(['A', 'B', 'C']);

  return (
    <div>
      <button onClick={() => setItems(['B', 'A', 'C'])}>Swap A and B</button>
      {items.map((item, index) => (
        <Item key={index} label={item} />
      ))}
    </div>
  );
}

```

### 문제 상황

- 사용자: 첫 번째 `Item`(label: A)의 input에 "hello" 입력
- 버튼 클릭: A와 B의 위치를 교체
- React는 `index` 기반 key로 인해 **첫 번째 컴포넌트를 그대로 유지**
- 결과적으로 첫 번째 컴포넌트는 label은 B지만 **state는 A의 상태인 "hello"를 가지고 있음**

```
의도: A의 입력값은 A에만 남아야 한다.
결과: B가 A의 상태를 들고 있다. → 상태 꼬임

```

---

## 해결: 안정적인 key 사용

```jsx
{items.map((item) => (
  <Item key={item} label={item} />
))}

```

`item`이 고유한 값이면 그 자체를 key로 사용하는 게 맞다. 이렇게 하면 컴포넌트가 정확하게 매칭되고, 상태도 원래대로 이어지게 된다.

---

## hook은 컴포넌트 인스턴스에 종속된다

React의 `useState` 같은 hook은 컴포넌트 내부에서 실행 순서에 따라 매칭된다. 그러나 이 hook이 어느 컴포넌트에 붙는지는 **그 컴포넌트 인스턴스가 유지되는가에 달려 있다.**

이 컴포넌트 인스턴스의 유지 여부는 **key가 같으면 유지, 다르면 새로 생성**으로 판별된다. 따라서 key가 틀리면 상태도 날아갈 수밖에 없다.

---

## 결론

- key는 React가 컴포넌트를 식별하는 기준이며, 이 식별이 정확해야 상태도 정확하게 이어질 수 있다.
- hook은 컴포넌트 함수 자체에 종속되는 게 아니라, 컴포넌트 인스턴스에 종속된다.
- 결국 key가 불안정하면 상태 꼬임이나 리렌더링 과잉이 발생할 수밖에 없다.