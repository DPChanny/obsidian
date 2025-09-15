---
type: library
related:
  - "[[Libraries/React/Component/Hook/Hook|Hook]]"
---
## 1. 개요

`useMutation`은 서버에 데이터를 **보내는** 동작을 담당한다.

POST, PUT, DELETE 요청처럼 **서버 상태를 변경하는 동작**을

React Query 내부에서 상태 기반으로 처리할 수 있게 만든 훅이다.

---

## 2. 기본 예제

```tsx
'use client'

import { useMutation, useQueryClient } from '@tanstack/react-query'

export default function PostAdd() {
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: async (title: string) => {
      await fetch('/api/posts', {
        method: 'POST',
        body: JSON.stringify({ title }),
      })
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['posts'] })
    },
  })

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    const formData = new FormData(e.currentTarget)
    const title = formData.get('title') as string
    mutation.mutate(title)
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="title" placeholder="제목" required />
      <button type="submit" disabled={mutation.isPending}>
        {mutation.isPending ? '전송 중...' : '제출'}
      </button>
    </form>
  )
}

```

---

## 3. 상태값

|상태|설명|
|---|---|
|`mutation.isPending`|요청 중일 때 true|
|`mutation.isSuccess`|요청이 성공했을 때 true|
|`mutation.isError`|요청이 실패했을 때 true|

- `mutation.mutate(data)`로 실행
- 상태값은 `useQuery`와 동일한 구조로 분기 가능

---

## 4. 캐시 갱신

- 데이터 전송 이후 UI를 갱신하려면 캐시를 무효화해야 함
- `queryClient.invalidateQueries({ queryKey: [...] })`로 지정한 쿼리 강제 갱신
- submit 성공 후 목록을 새로 불러오거나, 특정 데이터를 갱신하는 데 사용됨

---

## 5. 요약

|항목|설명|
|---|---|
|목적|서버로 데이터를 전송 (POST, PUT, DELETE 등)|
|사용 방식|`mutation.mutate(data)` 호출|
|상태 추적|isPending, isSuccess, isError|
|후속 처리|`onSuccess`에서 `invalidateQueries`로 캐시 갱신|

`useMutation`은 단순한 요청을 넘어서

**클라이언트 상태 흐름과 서버 상태 갱신을 연결하는 핵심 도구**다.

쓰기 동작은 `useMutation`, 읽기 동작은 `useQuery`로 명확히 분리된다.