---
type: framework
parent: "[[Frameworks/Next.js/App Router|App Router]]"
---
## 1. 개념

Intercepted Route는 현재 페이지의 레이아웃과 상태를 유지한 채,

병렬 슬롯(예: @side, @drawer 등)에만 새로운 콘텐츠를 렌더링하는 방식이다.

App Router는 기본적으로 경로가 바뀌면 전체 layout 트리를 재구성하지만,

Intercepted Route는 특정 병렬 슬롯만 변경하여 전체 context를 유지한 채 UI를 확장할 수 있게 한다.

---

## 2. `( .. )` 폴더의 의미

```
/app/@side/(..)/user/[id]/page.tsx

```

- `(..)`는 실제 폴더 이름이다
- 의미: 현재 경로 context(예: `/dashboard`)를 유지하고,
- 해당 콘텐츠를 병렬 슬롯에 렌더링하겠다는 뜻

---

## 3. 병렬 슬롯이 없으면 동작하지 않는다

```tsx
// /app/layout.tsx
export default function RootLayout({
  children,
  side,
}: {
  children: React.ReactNode;
  side: React.ReactNode;
}) {
  return (
    <div className="layout">
      <main>{children}</main>
      <aside>{side}</aside>
    </div>
  );
}

```

이처럼 layout에 `side` 슬롯이 정의돼 있어야만,

`/@side/(..)/...` Intercepted Route가 동작한다.

정의되지 않으면 아무 것도 렌더링되지 않고 무시된다.

---

## 4. 실전 구조 예시: 사이드 유저 정보 패널

```
/app
  ├── layout.tsx
  ├── dashboard
  │    ├── page.tsx
  │    └── user
  │         └── [id]
  │              └── page.tsx
  ├── @side
  │    ├── layout.tsx
  │    ├── page.tsx               ← (선택) 초기 상태
  │    └── (..)
  │         └── user
  │              └── [id]
  │                   └── page.tsx

```

---

## 5. 초기 상태 정의: Intercept 되기 전 콘텐츠 구성

```tsx
// /app/@side/page.tsx
export default function SideDefault() {
  return <div>유저를 선택하세요</div>;
}

```

- 병렬 슬롯은 처음에는 비어 있지만,
- 이렇게 `@side/page.tsx`를 정의하면 **Intercepted Route가 호출되기 전까지의 기본 콘텐츠**를 보여줄 수 있다
- 병렬 슬롯은 항상 존재하지만, 비어 있을 수도, 기본 콘텐츠가 있을 수도 있다

---

## 6. URL은 같아도 "경로 위치"에 따라 동작이 달라진다

|경로 위치|렌더링 방식|
|---|---|
|`/app/dashboard/user/[id]`|전체 페이지 전환|
|`/app/@side/(..)/user/[id]`|현재 페이지 유지 + side만 교체|

---

## 7. 구분 기준: 어떤 경로가 우선인가?

Next.js는 동일한 URL이 여러 경로에서 정의돼 있을 경우,

가장 가까운 파일 시스템 경로를 우선으로 사용한다.

즉, `/app/dashboard/user/[id]/page.tsx`가 존재하면

절대경로 `href="/dashboard/user/42"`는 이 route를 우선 적용한다.

---

## 8. Intercepted Route를 명시적으로 사용하려면?

상대 경로로 지정해야 한다.

```tsx
// /app/dashboard/page.tsx
<Link href="../user/42">유저 정보 보기</Link>

```

- 현재 경로 `/dashboard` 기준에서 `../user/42`로 이동하면,
- `/@side/(..)/user/[id]`가 탐색되어 side 슬롯에만 콘텐츠가 렌더링된다

절대경로는 일반 route를 우선한다.

```tsx
<Link href="/dashboard/user/42">전체 페이지 전환됨</Link>

```

---

## 9. 렌더링 흐름 요약

```
/app/layout.tsx
 ├── children = /dashboard/page.tsx
 └── side =
      ├── /@side/page.tsx (기본 콘텐츠)
      └── /@side/(..)/user/[id]/page.tsx (Intercepted 콘텐츠)

```

Next.js는 현재 페이지는 그대로 유지하면서 병렬 슬롯만 바꾸는 트리를 구성한다.

---

## 10. 요약

|항목|설명|
|---|---|
|`( .. )` 폴더|Intercepted Route 선언용, 상위 route context 유지|
|병렬 슬롯 필요|layout에서 `side`, `drawer` 등의 병렬 prop이 정의돼 있어야 함|
|경로 우선 순위|절대 경로는 일반 route 우선, Intercept는 상대 경로로 명시해야 함|
|기본 콘텐츠 구성|병렬 슬롯 내부에 `page.tsx` 작성 시 Intercept 전 상태 구성 가능|
|사용 조건|현재 위치에서 context를 유지할 수 있을 때만 Intercept 작동|

Intercepted Route는 병렬 레이아웃을 구조적으로 제어하는 도구다.

상태를 유지한 채 UI를 확장하고 싶다면, 이 구조를 활용할 수밖에 없다.