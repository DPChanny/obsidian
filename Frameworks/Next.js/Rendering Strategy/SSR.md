---
type: framework
---
## 1. 개요

SSR은 요청 시점에 서버에서 React 컴포넌트를 실행하여 HTML을 렌더링하고,

그 결과를 클라이언트로 전송하는 방식이다.

App Router에서는 `fetch()` 호출만으로 SSR이 동작하며,

클라이언트와 상호작용이 필요한 부분은 `'use client'` 컴포넌트로 분리해 처리한다.

---

## 2. 작동 흐름

1. 사용자가 SSR route에 접근
2. 서버에서 해당 React 컴포넌트를 실행하고 HTML 생성
3. 생성된 HTML을 클라이언트로 전송
4. 브라우저는 HTML을 그대로 렌더링
5. 이후 React가 hydration을 통해 상호작용 가능한 상태로 변환

> 서버에서 fetch는 매우 빠르고, 네트워크와 가까운 위치에서 실행되기 때문에
> 
> **데이터를 서버에서 먼저 받아와서 HTML에 “구워 넣은 채” 전달하는 게 훨씬 효율적이다.**

---

## 3. 예제 1: 문자열만 렌더링

```tsx
// app/dashboard/page.tsx
export default async function Page() {
  const res = await fetch('<https://jsonplaceholder.typicode.com/users>', {
    cache: 'no-store',
  })
  const users = await res.json()

  return <pre>{JSON.stringify(users, null, 2)}</pre>
}

```

- 서버에서 HTML 생성됨
- 클라이언트 상호작용 없음
- 정적인 출력에 적합

---

## 4. 예제 2: 서버에서 주입 → 클라이언트 컴포넌트로 렌더링

```tsx
// app/dashboard/page.tsx
import UserList from './UserList'

export default async function Page() {
  const res = await fetch('<https://jsonplaceholder.typicode.com/users>', {
    cache: 'no-store',
  })
  const users = await res.json()

  return <UserList users={users} />
}

```

```tsx
// app/dashboard/UserList.tsx
'use client'

export default function UserList({ users }: { users: any[] }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  )
}

```

- 서버에서 데이터를 받아 클라이언트 컴포넌트에 props로 전달
- 클라이언트 컴포넌트는 그대로 렌더링만 수행
- 동작은 빠르며 이후 동적 조작은 별도로 구성 가능

---

## 5. 예제 3: React Query + HydrationBoundary

```tsx
// lib/getUsers.ts
export async function getUsers() {
  const res = await fetch('<https://jsonplaceholder.typicode.com/users>', {
    cache: 'no-store',
  })
  return res.json()
}

```

```tsx
// app/dashboard/page.tsx
import { getUsers } from '@/lib/getUsers'
import { HydrationBoundary, QueryClient, dehydrate } from '@tanstack/react-query'
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

  if (isLoading) return <div>로딩 중...</div>

  return (
    <ul>
      {data.map((user: any) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  )
}

```

- SSR에서 React Query로 fetch + 캐시
- 클라이언트는 hydration을 통해 상태 복원
- 이후 `refetch`, `invalidate` 가능

> React Query는 클라이언트에서 재요청하지 않고
> 
> **서버에서 가져온 데이터를 context로 이어받아 바로 사용할 수 있게 만든다.**

---

## 6. 요약

|방식|특징|
|---|---|
|문자열 출력|정적 콘텐츠, 디버깅용|
|props 주입|빠른 렌더 + 단순 클라이언트 사용|
|React Query|상태 복원 + 조작 가능 + 네트워크 재요청 없음|

SSR은 단순한 서버 렌더링이 아니다.

클라이언트에 **상태를 구조적으로 전달하는 방법**을 결정하는 설계 수단이다.