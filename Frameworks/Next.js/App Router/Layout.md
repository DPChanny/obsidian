---
---
## 1. layout.tsx란 무엇인가

`layout.tsx`는 해당 디렉토리와 그 하위 경로에 공통으로 적용되는 UI 컴포넌트다.

라우팅 구조에 따라 계층적으로 중첩되며, 페이지 간 이동 시에도 그대로 유지된다.

이 구조는 시각적 계층과 URL 경로 구조를 일치시키며, 공통 UI를 효율적으로 재사용할 수 있게 만든다.

```tsx
// app/layout.tsx
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="ko">
      <head />
      <body>
        <Header />
        <main>{children}</main>
        <Footer />
      </body>
    </html>
  );
}

```

- 최상위 layout에서는 `html`, `head`, `body` 태그를 포함한 전체 HTML 스켈레톤을 구성해야 한다
- 반드시 `children`을 포함하여 하위 layout 또는 page가 렌더링될 수 있도록 해야 한다

---

## 2. 중첩 layout 구조 예시

다단계 layout 중첩 구조 예시:

```
/app
  └── layout.tsx                   → 전역 layout
  └── dashboard
       └── layout.tsx              → /dashboard 공통 layout
       └── page.tsx                → /dashboard
       └── users
            └── layout.tsx         → /dashboard/users 공통 layout
            └── page.tsx           → /dashboard/users
            └── [id]
                 └── page.tsx      → /dashboard/users/:id

```

### 각 layout 내용

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <section>
      <DashboardNav />
      <div>{children}</div>
    </section>
  );
}

```

```tsx
// app/dashboard/users/layout.tsx
export default function UsersLayout({ children }: { children: React.ReactNode }) {
  return (
    <div>
      <h2>유저 관리</h2>
      {children}
    </div>
  );
}

```

### 요청별 렌더링 흐름

- `/dashboard` 요청 시
    - `app/layout.tsx` → `dashboard/layout.tsx` → `dashboard/page.tsx`
- `/dashboard/users` 요청 시
    - `app/layout.tsx` → `dashboard/layout.tsx` → `users/layout.tsx` → `users/page.tsx`
- `/dashboard/users/123` 요청 시
    - `app/layout.tsx` → `dashboard/layout.tsx` → `users/layout.tsx` → `users/[id]/page.tsx`

📌 이때 `dashboard/page.tsx`는 `/dashboard` 요청에서만 호출되고, 하위 경로 접근 시에는 건너뛴다.

---

## 3. 렌더링 최적화와 layout의 성능

layout은 **한 번 렌더링된 이후에는 페이지 전환 시 다시 렌더링되지 않는다.**

예를 들어 `/dashboard/users` → `/dashboard/users/123`으로 이동할 경우:

```
app/layout.tsx (유지)
└─ dashboard/layout.tsx (유지)
   └─ users/layout.tsx (유지)
      └─ page 변경만 발생

```

따라서

- 공통 레이아웃은 캐시된 상태로 유지되고
- 실제 변경이 필요한 컴포넌트만 교체되므로
- 렌더링 최소화 → 성능 최적화

이런 구조로 인해 자연스럽게 상태가 보존되고, UI의 일관성도 확보된다.

---

## 4. 요약

- layout은 라우팅 구조와 함께 계층적으로 중첩되며, 해당 경로의 공통 UI를 구성한다
- `layout.tsx`는 반드시 `children`을 포함하며, 상위에서 하위로 중첩되어 최종 page.tsx를 감싼다
- 최상위 layout에서는 `html`, `head`, `body`까지 명시적으로 구성해야 한다
- 중간 디렉토리에 layout과 page가 함께 존재할 수 있으며, 해당 경로일 때만 page가 렌더링된다
- 하위 경로 접근 시 중간 page는 생략되고, layout만 중첩되어 적용된다
- layout은 렌더링 재사용을 통해 성능을 자연스럽게 최적화한다