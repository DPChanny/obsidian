---
---
## 1. 구조적 목표

Next.js App Router에서는 모달도 구조다.

여기서는 다음을 목표로 한다:

- 최상위 layout에서 병렬 슬롯 `@modal`을 정의
- `@modal/layout.tsx`에서 공통 모달 UI (제목/버튼) 구성
- 하위 route에서 `post/add`, `post/update`, `sensor/add` 등 실제 콘텐츠만 정의
- 모달은 Intercepted Route로 렌더링
- submit 처리 → router.replace()로 리디렉션
- `modal` 슬롯이 정의되지 않은 경우의 비동작 사례도 설명

---

## 2. 전체 구조

```
/app
  └── layout.tsx                     ← 최상위 layout: modal 슬롯 정의
  └── @modal
       └── layout.tsx                ← 모달 공통 레이아웃 (제목/버튼)
       └── (..)
            └── post
                 └── add/page.tsx     ← 게시물 추가 폼
                 └── update/page.tsx  ← 게시물 수정 폼
            └── sensor
                 └── add/page.tsx     ← 센서 등록 폼

```

---

## 3. 최상위 layout.tsx

```tsx
// app/layout.tsx
export default function RootLayout({
  children,
  modal,
}: {
  children: React.ReactNode;
  modal: React.ReactNode;
}) {
  return (
    <html>
      <body>
        {children}
        {modal} {/* 병렬 슬롯: 정의하지 않으면 modal이 무시됨 */}
      </body>
    </html>
  );
}

```

---

## 4. 모달 공통 레이아웃 (`@modal/layout.tsx`)

```tsx
'use client'

import { useRouter } from 'next/navigation'

export default function ModalLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  const router = useRouter()

  const handleSubmit = () => {
    const form = document.getElementById('modal-form') as HTMLFormElement
    if (form) form.requestSubmit()
  }

  return (
    <div className="modal-overlay">
      <div className="modal-box">
        <header><h2>모달</h2></header>
        <section>{children}</section>
        <footer>
          <button onClick={() => router.back()}>취소</button>
          <button onClick={handleSubmit}>확인</button>
        </footer>
      </div>
    </div>
  );
}

```

---

## 5. 게시물 추가 폼 (`post/add/page.tsx`)

```tsx
'use client'

import { useRouter } from 'next/navigation'

export default function PostAdd() {
  const router = useRouter()

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    const formData = new FormData(e.currentTarget)
    const title = formData.get('title')

    // 서버에 전송 또는 상태 처리 후
    console.log('제출됨:', title)
    router.replace('/dashboard') // 모달 닫기
  }

  return (
    <form id="modal-form" onSubmit={handleSubmit}>
      <input name="title" placeholder="제목" required />
    </form>
  );
}

```

---

## 6. 게시물 수정 폼 (`post/update/page.tsx`)

```tsx
'use client'

import { useRouter } from 'next/navigation'

export default function PostUpdate() {
  const router = useRouter()

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    const formData = new FormData(e.currentTarget)
    const title = formData.get('title')

    // 수정 처리
    console.log('수정됨:', title)
    router.replace('/dashboard') // 모달 닫기
  }

  return (
    <form id="modal-form" onSubmit={handleSubmit}>
      <input name="title" defaultValue="기존 제목" required />
    </form>
  );
}

```

---

## 7. 센서 등록 폼 (`sensor/add/page.tsx`)

```tsx
'use client'

import { useRouter } from 'next/navigation'

export default function SensorAdd() {
  const router = useRouter()

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    const formData = new FormData(e.currentTarget)
    const id = formData.get('sensorId')

    console.log('센서 등록:', id)
    router.replace('/dashboard')
  }

  return (
    <form id="modal-form" onSubmit={handleSubmit}>
      <input name="sensorId" placeholder="센서 ID" required />
    </form>
  );
}

```

---

## 8. 대시보드에서 모달 열기 (`/dashboard/page.tsx`)

```tsx
'use client'

import Link from 'next/link'

export default function Dashboard() {
  return (
    <>
      <h1>대시보드</h1>
      <Link href="/dashboard/post/add">게시물 추가</Link>
      <Link href="/dashboard/post/update">게시물 수정</Link>
      <Link href="/dashboard/sensor/add">센서 등록</Link>
    </>
  );
}

```

---

## 9. 동작 흐름 요약

1. `/dashboard`에서 링크 클릭 → `/dashboard/post/add`로 이동
2. Intercepted Route가 `@modal` 슬롯에만 페이지 렌더링
3. 기존 화면 유지, 모달 오버레이로 겹쳐짐
4. 확인 버튼 → `modal-form` submit
5. 각 폼에서 처리 → `router.replace()`로 `/dashboard` 복귀

---

## 10. 요약

- modal은 반드시 layout에서 병렬 슬롯으로 선언돼야 한다
- @modal/layout.tsx는 재사용 가능한 wrapper이며, submit/취소 제어 담당
- 하위 route는 각각의 폼만 정의하고, 공통 레이아웃을 활용
- Intercepted Route로 현재 경로를 유지하면서 모달을 겹쳐 띄운다
- 정의되지 않은 modal 슬롯은 Intercepted Route를 무시하고 아무 것도 렌더링하지 않는다

App Router에서 모달은 구조의 일부이며, **레이아웃 수준에서 관리해야 한다.**