---
type: library
related:
  - "[[Libraries/React/Component/Context|Context]]"
---
## 1. 개요

Hydration은 서버에서 미리 가져온 React Query 상태를

클라이언트로 그대로 전달해서, **재요청 없이 바로 캐시된 데이터를 사용**하게 하는 방식이다.

---

## 2. 최소 예제

```tsx
// lib/getUsers.ts
export async function getUsers() {
  const res = await fetch('<https://jsonplaceholder.typicode.com/users>')
  return res.json()
}

```

```tsx
// app/dashboard/page.tsx
import { HydrationBoundary, dehydrate, QueryClient } from '@tanstack/react-query'
import { getUsers } from '@/lib/getUsers'
import DashboardClient from './DashboardClient'

export default async function Page() {
  const queryClient = new QueryClient()

  await queryClient.prefetchQuery({
    queryKey: ['users'],
    queryFn: getUsers,
  })

  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      <DashboardClient />
    </HydrationBoundary>
  )
}

```

```tsx
// app/dashboard/DashboardClient.tsx
'use client'

import { useQuery } from '@tanstack/react-query'
import { getUsers } from '@/lib/getUsers'

export default function DashboardClient() {
  const { data, isLoading } = useQuery({
    queryKey: ['users'],
    queryFn: getUsers,
  })

  if (isLoading) return <p>로딩 중...</p>

  return (
    <ul>
      {data.map((user: any) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  )
}

```

---

## 3. 핵심 포인트

- `prefetchQuery`로 쿼리를 실행하면 내부 캐시에 저장된다
- `dehydrate`로 이 캐시를 직렬화된 JSON으로 추출
- `HydrationBoundary`가 이를 context로 전달
- 클라이언트의 `useQuery`는 동일한 `queryKey`를 기준으로 자동 매칭하여 재요청 없이 데이터를 사용한다

---

## 4. 요약

|항목|설명|
|---|---|
|목적|SSR에서 받은 데이터를 CSR에서 재사용|
|기준|queryKey로 매칭됨|
|구성 요소|`prefetchQuery`, `dehydrate`, `HydrationBoundary`|

Hydration은 React Query의 SSR + CSR 연결을 가능하게 하는 기본 구조다.

데이터를 "다시 안 받아도 되게 만드는" 구조라고 이해하면 된다.