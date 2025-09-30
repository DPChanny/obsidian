---
related:
  - "[[Frameworks/Next.js/App Router/Layout|Layout]]"
---
## 1. 병렬 레이아웃이란?

Next.js App Router의 병렬 레이아웃(Parallel Routes)은 하나의 layout에서 여러 개의 하위 route를 **동시에 구성하는 방식**이다.

전통적인 중첩 layout이 하나의 `children`만 렌더링하는 구조였다면, 병렬 layout은 **슬롯(slot)**이라는 개념을 통해 **여러 하위 콘텐츠를 병렬로 분리**한다.

---

## 2. 구조와 동작 방식

### 디렉토리 구조 예시

```
/app
  └── dashboard
       └── layout.tsx
       └── @main
            └── page.tsx        → 메인 콘텐츠
       └── @side
            └── page.tsx        → 사이드 영역

```

- `@main`, `@side`는 병렬 슬롯
- 이들은 각각의 route처럼 독립되지만 같은 layout 하위에서 병렬로 렌더링됨

### layout.tsx 예시

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({
  main,
  side,
}: {
  main: React.ReactNode;
  side: React.ReactNode;
}) {
  return (
    <div style={{ display: 'flex' }}>
      <section>{main}</section>
      <aside>{side}</aside>
    </div>
  );
}

```

### 렌더링 흐름

`/dashboard`로 진입하면 다음이 동시에 렌더링된다:

- `dashboard/layout.tsx`
- `@main/page.tsx` → `main` 슬롯으로
- `@side/page.tsx` → `side` 슬롯으로

슬롯별 콘텐츠가 병렬로 구성되어 하나의 레이아웃 안에 나란히 표시된다.

---

## 3. 특징

- 각 슬롯은 **독립된 route**로 동작하므로 서로 다른 상태와 경로를 가질 수 있다
- 동시에 렌더링되며, 전체 layout 구조를 유지하면서 **복합적인 UI 구성이 가능**하다
- 전통적인 layout의 한계를 극복하여 **다중 콘텐츠 병렬 처리**가 가능해진다

---

## 4. 사용 사례

- 대시보드처럼 **좌우 분할 UI**를 구성할 때
- **리스트 + 상세** 페이지를 병렬로 구성할 때
- 사이드 위젯, 광고 영역 등 독립된 콘텐츠를 고정 렌더링할 때

---

## 5. 요약

|항목|설명|
|---|---|
|구조 방식|슬롯 이름으로 병렬 디렉토리 구성 (`@name`)|
|layout props|`slotName: React.ReactNode`|
|렌더링 흐름|layout + 각 슬롯 page 동시 렌더링|
|주요 장점|UI 분리, 병렬 처리, 상태 격리|
|주의점|슬롯 이름과 layout props 명시 일치 필요|

---

병렬 레이아웃은 App Router의 구조적 유연성을 극대화하는 도구다.

여러 개의 콘텐츠를 병렬로 렌더링하면서도 구조를 명확히 유지할 수 있어, 복잡한 UI를 정돈된 방식으로 구성할 수밖에 없다.