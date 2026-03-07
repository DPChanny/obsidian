---
---
## 1. 기본 개념

App Router에서는 파일 또는 폴더 이름에 대괄호(`[ ]`)를 사용해 **동적 경로 세그먼트**를 정의할 수 있다.

해당 URL의 일부를 변수처럼 처리하며, 컴포넌트에서는 이를 `params` 객체를 통해 접근한다.

---

### 예시 구조

```
/app
  └── blog
       └── [slug]
            └── page.tsx

```

요청 URL: `/blog/hello-world`

`params.slug` → `"hello-world"`

```tsx
export default function BlogPost({ params }: { params: { slug: string } }) {
  return <h1>{params.slug}</h1>;
}

```

---

## 2. 중첩 동적 세그먼트

동적 세그먼트는 **중첩된 구조**로도 자유롭게 사용할 수 있다.

각 경로 세그먼트마다 별도의 `[param]` 폴더를 만들 수 있으며,

해당 depth에서 필요한 params만 자동으로 분리되어 전달된다.

---

### 예시

```
/app
  └── product
       └── [category]
            └── layout.tsx
            └── [id]
                 └── page.tsx

```

URL: `/product/shoes/123`

```tsx
// page.tsx
export default function ProductDetail({
  params,
}: {
  params: { category: string; id: string };
}) {
  return (
    <div>
      <p>카테고리: {params.category}</p>
      <p>상품 ID: {params.id}</p>
    </div>
  );
}

```

- `params`는 모든 상위 동적 세그먼트를 병합해서 제공된다
- URL 계층이 고정되어 있다면 이 방식이 가장 명확하고 관리하기 쉽다

---

## 3. catch-all 및 optional catch-all

### `[...param]` → 필수 다중 세그먼트

```
/app/docs/[...slug]/page.tsx

```

|URL|`params.slug`|
|---|---|
|`/docs/a/b`|`["a", "b"]`|

---

### `[[...param]]` → 선택적 다중 세그먼트

```
/app/help/[[...page]]/page.tsx

```

|URL|`params.page`|
|---|---|
|`/help`|`undefined` 또는 `[]`|
|`/help/a`|`["a"]`|

---

### 실전에서 거의 쓰이지 않는 이유

- URL 구조가 명확하지 않아 유지보수에 불리
- 컴포넌트 내부 로직이 복잡해짐 (`params.slug`가 배열)
- 정적 생성, 메타데이터 구성 등이 어려워짐

**실제 사용 예**는 CMS, Markdown 문서 시스템 등 **비정형 URL 구조**가 필요한 경우에만 해당된다.

**대부분의 웹 앱은 depth가 명확히 정해진 `[param]` 방식으로 충분**하다.

---

## 4. generateStaticParams()

정적 생성 시, 빌드 타임에 사용할 경로 값을 명시할 수 있다.

```tsx
export async function generateStaticParams() {
  return [{ slug: 'a' }, { slug: 'b' }];
}

```

---

## 5. notFound() 처리

존재하지 않는 param 값에 대해 404 처리를 위해 `notFound()`를 호출한다.

```tsx
import { notFound } from 'next/navigation'

export default function Page({ params }: { params: { slug: string } }) {
  const post = getPost(params.slug)
  if (!post) notFound()
  return <div>{post.title}</div>
}

```

---

## 6. 요약

|패턴|설명|예시 URL|`params` 값|
|---|---|---|---|
|`[id]`|하나의 세그먼트|`/user/1`|`{ id: "1" }`|
|`[...slug]`|여러 세그먼트 (필수)|`/docs/a/b`|`{ slug: ["a", "b"] }`|
|`[[...slug]]`|여러 세그먼트 (선택적)|`/docs`|`{ slug: [] }` or `undefined`|
|`[category]/[id]`|중첩 동적 세그먼트|`/product/shoes/123`|`{ category: "shoes", id: "123" }`|

---

## 7. 현실적인 권장사항

- 대부분의 서비스에서는 `[slug]`, `[id]`, `[category]/[id]` 같은 **단일 또는 중첩 동적 세그먼트**만으로 충분하다
- `[...slug]`, `[[...slug]]`는 CMS나 문서형 앱 외에는 가급적 피하는 것이 유지보수에 유리하다
- 라우팅은 **명확하게 설계된 계층 구조**로 고정하는 것이 실전에서는 안전하다