---
type: framework
parent: "[[Frameworks/Next.js/Rendering Strategy|Rendering Strategy]]"
---
## 1. 개요

CSR은 브라우저에서 React가 컴포넌트를 실행하여 렌더링하는 방식이다.

Next.js에서는 기본적으로 서버 컴포넌트 구조가 적용되므로,

**CSR을 사용하려면 `'use client'` 선언이 반드시 필요하다.**

> 원래 React는 기본적으로 CSR이다.
> 
> Next.js는 구조적으로 CSR을 제한하여 **필요한 곳에만 명시적으로 쓰게 만든다.**

---

## 2. 언제 사용하는가?

- `useState`, `useEffect`, `useRef` 등 클라이언트 훅을 사용할 때
- 사용자 입력 처리 (폼, 버튼, 토글 등)
- 브라우저 API 접근 (window, localStorage 등)
- **React Query**로 클라이언트 상태 + 데이터 fetch 조합이 필요할 때

---

## 3. 구조적 특징

> CSR은 사실상 컴포넌트 단위에서만 선언된다.

- `page.tsx`, `layout.tsx`는 기본적으로 서버 컴포넌트
- `'use client'`는 작은 UI 단위에서만 작성
- 상태/입력/클라이언트 fetch가 필요한 최소 단위를 CSR로 분리

---

## 4. React Query 예제

```tsx
// lib/getPosts.ts
export async function getPosts() {
  const res = await fetch('<https://jsonplaceholder.typicode.com/posts>')
  return res.json()
}

```

```tsx
// app/posts/ClientPosts.tsx
'use client'

import { useQuery } from '@tanstack/react-query'
import { getPosts } from '@/lib/getPosts'

export default function ClientPosts() {
  const { data, isLoading } = useQuery({
    queryKey: ['posts'],
    queryFn: getPosts,
  })

  if (isLoading) return <div>로딩 중...</div>

  return (
    <ul>
      {data.slice(0, 5).map((p: any) => (
        <li key={p.id}>{p.title}</li>
      ))}
    </ul>
  )
}

```

- fetch는 React Query 내부에서 처리
- 클라이언트 상태로 관리되며 refetch, cache 관리 가능
- 전역 context 기반이라 구조 확장성도 높음

---

## 5. SSR과의 혼용 예시

```tsx
// app/dashboard/page.tsx
import ClientPosts from '../posts/ClientPosts'

export default function DashboardPage() {
  return (
    <div>
      <h1>대시보드</h1>
      <ClientPosts />
    </div>
  )
}

```

- 전체 페이지는 SSR로 렌더링됨
- 특정 컴포넌트만 CSR로 분리하여 브라우저 기반 상호작용을 수행

---

## 6. 요약

|항목|설명|
|---|---|
|선언 방식|`'use client'` 파일 상단|
|적용 대상|컴포넌트 단위 전용|
|동작 위치|브라우저|
|데이터 요청|`useQuery()` 기반 CSR 상태 관리|
|SSR 혼용|일부 UI만 CSR로 분리하여 사용 가능|

CSR은 더 이상 전체 페이지를 구성하는 기본이 아니다.

Next.js에서는 구조적으로 CSR을 분리하고,

**상태와 상호작용 중심의 UI만 CSR로 명확히 선언하는 방식**을 강제한다.

그 내부 로직은 React Query와 함께 구성하는 것이

**성능, 유지보수, UX 모든 면에서 구조적으로 낫다.**