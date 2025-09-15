---
type: library
---
## 1. queryClient란?

React Query의 queryClient는

**쿼리 상태, 캐시, fetch 흐름을 모두 관리하는 중앙 컨트롤러**다.

```tsx
const queryClient = new QueryClient()

```

- 모든 쿼리(useQuery, useMutation)는 queryClient를 기반으로 작동한다
- `QueryClientProvider`로 앱 전체에 주입되어야 동작한다

---

## 2. 전역 queryClient – CSR에서의 기본 구조

React Query는 **하나의 queryClient 인스턴스를 전역으로 공유**하는 구조를 기본으로 한다.

```tsx
<QueryClientProvider client={queryClient}>
  <App />
</QueryClientProvider>

```

- useQuery, useMutation 등은 내부적으로 이 context의 queryClient를 참조한다
- 실제 캐시 처리도 이 인스턴스 안에서 모두 이루어진다

---

## 3. 실전 예: Next.js App Router 구조

```tsx
// app/layout.tsx
'use client'

import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { useState } from 'react'

export default function RootLayout({ children }) {
  const [queryClient] = useState(() => new QueryClient())

  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  )
}

```

- layout.tsx는 `'use client'`이지만 괜찮다
- 하위 서버 컴포넌트들은 여전히 SSR된다
- React Query의 클라이언트 상태를 위한 설정은 클라이언트 컴포넌트에서 감싸는 것이 정석

---

## 4. SSR에서는 queryClient를 별도로 생성

```tsx
// app/dashboard/page.tsx
import { dehydrate, HydrationBoundary, QueryClient } from '@tanstack/react-query'
import { getUsers } from '@/lib/getUsers'
import DashboardClient from './DashboardClient'

export default async function Page() {
  const queryClient = new QueryClient()
  await queryClient.prefetchQuery({ queryKey: ['users'], queryFn: getUsers })

  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      <DashboardClient />
    </HydrationBoundary>
  )
}

```

- 이 queryClient는 **요청 단위로 새로 생성**된다
- `dehydrate()`로 상태를 직렬화해서 클라이언트에 넘김

---

## 5. 클라이언트에서는 context 기반의 queryClient 사용

```tsx
// app/dashboard/DashboardClient.tsx
'use client'

import { useQuery } from '@tanstack/react-query'
import { getUsers } from '@/lib/getUsers'

export default function DashboardClient() {
  const { data } = useQuery({
    queryKey: ['users'],
    queryFn: getUsers,
  })

  return <ul>{data.map(u => <li key={u.id}>{u.name}</li>)}</ul>
}

```

- 이 `useQuery()`는 자동으로 `QueryClientProvider`에서 주입된 queryClient를 사용
- 별도로 생성하지 않아도 된다
- `HydrationBoundary`는 이 context queryClient에 상태를 병합한다

---

## 6. 내부 구조

- `useQuery()`는 내부적으로 `useQueryClient()`를 호출한다
- `useQueryClient()`는 현재 context에 있는 queryClient를 반환
- 따라서 context 없이 `useQuery()`를 호출하면 에러가 발생한다

---

## 7. queryClient가 여러 개일 수는 있지만 일반적이지 않다

- 기능별로 여러 개의 queryClient를 둘 수는 있지만, 상태 충돌을 피하려면 **queryKey로 구분하는 것이 일반적**
- SSR에서는 요청 단위로 queryClient가 격리되기 때문에 문제가 되지 않음
- CSR에서는 전역 단일 queryClient + 계층적 queryKey가 표준 구조

---

## 8. 요약

|항목|설명|
|---|---|
|queryClient|React Query의 전역 상태 컨트롤러|
|Provider|반드시 앱 루트에서 설정 필요|
|Hydration|Provider 내부에서만 동작|
|useQuery|내부적으로 queryClient 사용|
|SSR|요청 단위로 queryClient 생성 후 hydrate|
|queryKey|상태 구분의 기준, 구조적으로 설계 필수|
|layout.tsx|'use client'여도 SSR 하위 구조는 유지됨|

queryClient는 React Query의 핵심 기반이다.

**전역 상태 구조, SSR 격리, CSR 캐시 공유, UI 재실행 트리거 전부가 이 객체를 중심으로 작동한다.**