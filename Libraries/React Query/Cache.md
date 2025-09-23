---
type: library
parent: "[[Libraries/React Query|React Query]]"
---
## 1. 캐시 개요

React Query는 서버 데이터를 `queryKey`를 기준으로 캐시하고,

`useQuery()`는 해당 키의 캐시를 구독한다.

이 캐시에 변화가 생기면 컴포넌트는 자동으로 재렌더링된다.

---

## 2. invalidateQueries – 캐시 무효화 후 재요청

```tsx
queryClient.invalidateQueries({ queryKey: ['posts'] })

```

- 캐시를 stale 상태로 만들고
- 연결된 `useQuery(['posts'])`가 자동으로 `queryFn`을 재실행
- 네트워크 요청이 다시 발생함

### 예제

```tsx
const mutation = useMutation({
  mutationFn: (data) => fetch('/api/posts', {
    method: 'POST',
    body: JSON.stringify(data)
  }),
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['posts'] })
  }
})

```

- 서버에 데이터 전송 후
- 목록을 다시 불러와서 정확하게 동기화

---

## 3. setQueryData – 캐시 직접 수정으로 실시간 반영

```tsx
queryClient.setQueryData(['posts'], (old) => [...old, newPost])

```

- 네트워크 요청 없이 클라이언트 캐시만 변경
- 구독 중인 `useQuery()` 컴포넌트는 즉시 데이터 반영
- 서버 요청은 이미 성공했으므로 **재요청 없이 신뢰하고 반영**

### 예제

```tsx
const mutation = useMutation({
  mutationFn: async (newPost) => {
    const res = await fetch('/api/posts', {
      method: 'POST',
      body: JSON.stringify(newPost),
    })
    return res.json()
  },
  onSuccess: (createdPost) => {
    queryClient.setQueryData(['posts'], (old: any[]) => {
      return [...(old ?? []), createdPost]
    })
  }
})

```

- 게시글을 서버에 전송하고,
- 응답값을 그대로 캐시에 추가
- 전체 목록을 다시 받아오지 않고 UI에 **즉시 반영**

---

## 4. 언제 invalidate, 언제 setQueryData?

|상황|선택|
|---|---|
|목록 정렬/필터가 적용돼 있음|invalidateQueries|
|서버 응답 구조를 정확히 모름|invalidateQueries|
|전체 재요청이 부담되지 않음|invalidateQueries|
|응답값이 그대로 목록에 들어갈 수 있음|setQueryData|
|즉시 UI 반응이 필요한 UX (채팅, 알림 등)|setQueryData|
|로컬 상태처럼 제어하고 싶음|setQueryData|

---

## 5. 결론

> 유저 추가, 포스트 생성 등 결과를 예측할 수 있는 단순 목록 구조에서는
> 
> `setQueryData()`로 **즉시 캐시를 수정하는 것이 더 빠르고 부드러운 UX**를 만든다.

반면

> 서버 상태를 다시 정확히 확인해야 하는 복잡한 구조에서는
> 
> `invalidateQueries()`로 전체 동기화를 거는 게 정확하다.

이렇게 React Query는

**정확성 vs 반응성** 사이의 균형을 개발자가 구조적으로 선택할 수 있게 해준다.