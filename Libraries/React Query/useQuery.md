---
related:
  - "[[Libraries/React/Component/Hook|Hook]]"
---
## 1. 개요

`useQuery`는 React Query의 핵심 훅이다.

데이터를 가져오고(fetch), 상태로 관리하고(state), 그 상태 변화에 따라 컴포넌트를 자동 재렌더링하는 훅이다.

```tsx
const { data, isLoading, error } = useQuery({
  queryKey: ['users'],
  queryFn: () => fetch('/api/users').then(res => res.json()),
})

```

---

## 2. 상태와 동작 시점

|상태|설명|첫 요청 중|refetch 중|refetch 후 성공|refetch 후 실패|
|---|---|---|---|---|---|
|`isLoading`|첫 로딩 시 true, 이후엔 항상 false|true|false|false|false|
|`isFetching`|fetch 중엔 항상 true, refetch 포함|true|true|false|false|
|`isSuccess`|최근 요청이 성공했다면 true, 유지됨|false→true|true|true|false|
|`isError`|최근 요청이 실패했다면 true|false|false|false|true|

- 각 상태는 독립적인 boolean 값이며, 현재 상황을 반영한다
- 여러 상태를 조합하여 조건 분기 가능

---

## 3. 구조 분해 방식 비교: useState vs useQuery

### useState – 배열 구조 분해

```tsx
const [count, setCount] = useState(0)

```

- 배열 구조 분해
- 각 변수명을 마음대로 지정 가능
- 이름 충돌 없음

---

### useQuery – 객체 구조 분해

```tsx
// 변수명 중복으로 에러 발생
const { data, isLoading } = useQuery({ queryKey: ['a'], queryFn: fetchA })
const { data, isLoading } = useQuery({ queryKey: ['b'], queryFn: fetchB })

```

- 객체 구조 분해
- 고정된 키 이름으로 반환되므로 이름 충돌 가능성 있음

---

## 4. 해결 방법

### 방법 1: 구조 분해 없이 변수로 전체 받기

```tsx
const queryA = useQuery({ queryKey: ['a'], queryFn: fetchA })
const queryB = useQuery({ queryKey: ['b'], queryFn: fetchB })

return (
  <>
    {queryA.isLoading ? '로딩 중 A' : queryA.data[0]}
    {queryB.isLoading ? '로딩 중 B' : queryB.data[0]}
  </>
)

```

---

### 방법 2: 구조 분해하면서 별칭(alias) 사용

```tsx
const { data: dataA, isLoading: loadingA } = useQuery({ queryKey: ['a'], queryFn: fetchA })
const { data: dataB, isLoading: loadingB } = useQuery({ queryKey: ['b'], queryFn: fetchB })

```

- 이름 충돌 방지
- 명시적이고 가독성 높음

---

## 5. 요약

|항목|설명|
|---|---|
|목적|데이터를 가져오고 상태처럼 다룸|
|작동 방식|상태 변화마다 컴포넌트 자동 재렌더링|
|반환 값|data, isLoading, error, refetch 등|
|구조 분해|객체 기반 → 이름 충돌 가능성 있음|
|대응 방식|alias 사용 또는 전체 객체로 참조|

useQuery는 React Query의 핵심이며,

서버 요청을 UI 상태로 통합하는 구조적 fetch 시스템이다.