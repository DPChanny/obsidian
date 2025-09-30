---
---
App Router는 라우팅 구조 안에서 특정 상황에 자동으로 호출되는 **특수 파일**들을 지원한다.

이 파일들은 각기 다른 목적과 시점을 가지고 호출되며, 글로벌 또는 로컬하게 적용할 수 있다.

구조적으로는 `layout.tsx`나 `page.tsx`와 동일하게 **폴더 단위**에서 사용된다.

---

## 1. `loading.tsx`

### 역할

- 해당 경로 또는 하위 컴포넌트에서 **`Suspense` 또는 lazy 로딩 중일 때** 렌더링된다
- SSR이 완료되기 전까지 사용자에게 **로딩 UI**를 제공한다

### 전역 vs 로컬

- `/app/loading.tsx` → 모든 페이지 로딩 시 적용
- `/app/blog/loading.tsx` → `/blog` 하위 페이지에만 적용

### 예시

```tsx
// app/dashboard/loading.tsx
export default function Loading() {
  return <p>대시보드를 불러오는 중입니다...</p>;
}

```

---

## 2. `error.tsx`

### 역할

- 해당 경로 이하에서 컴포넌트 렌더링 중 **예외가 발생하면** 자동으로 fallback
- `error.tsx`는 `error` 객체를 받는 클라이언트 컴포넌트여야 한다

### 전역 vs 로컬

- `/app/error.tsx` → 앱 전체 예외 처리
- `/app/blog/error.tsx` → `/blog` 하위에서만 예외 처리

### 예시

```tsx
// app/blog/error.tsx
'use client'

export default function BlogError({ error }: { error: Error }) {
  return <p>블로그를 불러오는 중 오류가 발생했습니다: {error.message}</p>;
}

```

---

## 3. `not-found.tsx`

### 역할

- 명시적으로 `notFound()` 호출 시 렌더링
- fallback 라우트가 없거나 동적 경로에 해당 파일이 없을 때도 자동 렌더링

### 전역 vs 로컬

- `/app/not-found.tsx` → 전체 앱의 404
- `/app/products/not-found.tsx` → `/products` 전용 404

### 예시

```tsx
// app/not-found.tsx
export default function NotFound() {
  return <h1>페이지를 찾을 수 없습니다.</h1>;
}

```

```tsx
// app/products/[id]/page.tsx
import { notFound } from 'next/navigation'

export default function ProductPage({ params }: { params: { id: string } }) {
  const product = getProduct(params.id)
  if (!product) notFound()
  return <div>{product.name}</div>
}

```

---

## 4. `template.tsx`

### 역할

- `layout.tsx`와 유사하지만, **페이지 전환 시 상태가 초기화된다**
- 동일한 layout을 쓰되, **페이지마다 상태가 초기화돼야 하는 경우** 사용
- 예: Form, Modal 등 상태를 공유하면 안 되는 경우

### 예시

```tsx
// app/forms/template.tsx
export default function FormTemplate({ children }: { children: React.ReactNode }) {
  return <section>{children}</section>;
}

```

> 같은 route 아래 여러 page를 전환할 때 layout의 상태 공유를 원하지 않을 경우 layout.tsx 대신 template.tsx 사용

---

## 5. 요약

|파일명|역할|상태 유지|전역/로컬 가능|호출 조건|
|---|---|---|---|---|
|`loading.tsx`|로딩 중 UI|O|O|Suspense/lazy|
|`error.tsx`|예외 처리 UI|O|O|렌더링 중 예외 발생|
|`not-found.tsx`|404 처리|O|O|notFound() 또는 미존재 경로|
|`template.tsx`|layout처럼 감싸지만 상태 초기화|X|O|layout과 동일하나 상태 초기화 필요|

---

## 구조적 정리

- 이 특수 파일들은 모두 `layout.tsx`, `page.tsx`처럼 **디렉토리 기준**으로 적용된다
- 전역으로 두면 모든 route에 적용되고, 특정 디렉토리에 두면 해당 경로 이하에서만 작동한다
- App Router는 **구조에 따라 상황별 렌더링을 자동 분기**하는 방식이므로, 개발자는 조건에 맞는 파일만 제 위치에 배치하면 된다