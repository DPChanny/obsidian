---
type: framework
---
# Next.js App Router 개요와 철학

## 1. App Router는 단순한 라우터가 아니다

Next.js의 App Router는 단순히 URL과 컴포넌트를 연결하는 라우터가 아니다.

**UI의 구조를 코드로 강제하는 방식**이며, React의 “UI is a function of state”를 넘어 **“UI is a function of structure”**라는 철학을 따른다.

폴더 구조로 URL과 layout을 정의하게 되면, 개발자는 어쩔 수 없이 **구조적 사고**를 하게 된다.

이는 단순한 기능 구현을 넘어 **계층적 UI 설계**를 유도한다.

---

## 2. 디렉토리 = URL = UI 계층

App Router는 다음을 전제로 한다:

- `/app/foo/page.tsx` → `/foo` 페이지
- 각 디렉토리마다 layout.tsx를 둘 수 있고, 이는 중첩된다
- 파일 시스템이 곧 URL이고, 이는 곧 UI 구조다

```
/app
  └── layout.tsx            → 전체 layout
  └── dashboard
       └── layout.tsx       → /dashboard layout
       └── analytics
            └── page.tsx    → /dashboard/analytics

```

**URL 설계 = 디렉토리 설계 = UI 설계**

세 가지가 분리되지 않기 때문에, 구조는 언제나 명확하고 확장 가능해야 한다.

---

## 3. layout 중심의 계층 UI 구조

App Router의 핵심은 layout.tsx다.

- layout은 공통 UI 구조를 담당한다
- 중첩될 수 있으며, 렌더링은 상위 → 하위 → page 순
- 상위 layout은 리렌더링되지 않고 그대로 유지된다
- 구조만으로 탭, 섹션, 네비게이션 UI가 자연스럽게 정의된다

**따라서**, 화면 이동 시에도 구조는 그대로 유지되고, 상태도 보존되며, 렌더링은 필요한 부분만 발생한다.

---

## 4. 나의 개발 철학과 App Router

나는 언제나 **확장을 고려하고**, **사용자에게 최대한의 자유를 보장하는** 구조를 추구한다.

App Router는 강력한 제약을 통해 구조적 사고를 강요하지만,

그 제약 안에서 만든 구조는 되려 더 **유연하고, 안정적이며, 확장 가능하다.**

- 중첩 layout을 통해 구조를 무한히 계층화할 수 있다
- URL은 구조를 그대로 반영하므로, 사용자는 예측 가능한 UI 흐름을 경험하게 된다
- 필요한 수준만큼 세분화하고, 필요한 위치에서 캡슐화할 수 있다

**자유는 무질서가 아니라, 명확한 구조 안에서만 보장될 수밖에 없다.**

App Router는 그 구조를 코드에 각인시키는 방식이며, 내 철학과 정확히 맞닿아 있다.

---

## 5. 요약

- App Router는 URL과 UI 구조를 동일하게 설계하게 만든다
- layout.tsx는 계층 구조를 구성하고 상태와 렌더링을 최적화한다
- 디렉토리 구조는 곧 사용자의 경험 구조가 된다
- 구조를 명시하는 방식이야말로, 확장성과 자유를 함께 지킬 수 있는 유일한 해답이다