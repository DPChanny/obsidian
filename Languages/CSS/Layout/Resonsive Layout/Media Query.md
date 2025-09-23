---
type: language
parent: "[[Languages/CSS/Layout/Responsive Layout|Responsive Layout]]"
---
## 개요

CSS에서 반응형 레이아웃은 **화면 너비(뷰포트)에 따라 스타일을 바꾸는 방식**이다.

기기 크기에 따라 UI를 최적화하려면 `@media`를 통한 **조건부 스타일 분기**가 필수다.

```css
@media (max-width: 768px) {
  /* 화면 너비가 768px 이하일 때 적용 */
}

```

---

## 뷰포트 & 단위 기준

### 뷰포트란?

> 사용자가 보는 화면 영역.
> 
> 데스크탑, 태블릿, 모바일마다 너비가 다르다.

### 단위 차이

- `px`: 고정 크기 (정확하지만 유연하지 않음)
- `em`, `rem`: 폰트 기준 상대 단위 (반응형에서 선호)
- `%`: 부모 기준, 레이아웃에 유용

---

## 미디어쿼리 기본 구조

```css
@media (min-width: 768px) {
  /* 태블릿 이상 */
}

@media (max-width: 767px) {
  /* 모바일 */
}

```

|조건|의미|
|---|---|
|`max-width`|해당 너비 이하에만 적용|
|`min-width`|해당 너비 이상에만 적용|

---

## 예제 1: Grid 반응형 레이아웃

```html
<div class="grid">
  <div class="card">Card</div>
  <div class="card">Card</div>
  <div class="card">Card</div>
</div>

```

```css
.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}

@media (max-width: 1024px) {
  .grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 600px) {
  .grid {
    grid-template-columns: 1fr;
  }
}

```

```
→ 데스크탑: 3열
→ 태블릿: 2열
→ 모바일: 1열

```

---

## 예제 2: Flex 반응형 정렬

```html
<div class="nav">
  <a href="#">Home</a>
  <a href="#">About</a>
  <a href="#">Contact</a>
</div>

```

```css
.nav {
  display: flex;
  justify-content: space-between;
}

@media (max-width: 600px) {
  .nav {
    flex-direction: column;
    gap: 10px;
  }
}

```

```
→ 넓을 땐 가로 메뉴
→ 좁으면 세로로 접힘

```

---

## 실무 브레이크포인트 기준 예시

|기기|브레이크포인트|용도|
|---|---|---|
|Mobile|`max-width: 600px`|단일 열, 큰 텍스트|
|Tablet|`max-width: 1024px`|2열 또는 중간 정렬|
|Desktop|기본|3~4열 배치|

---

## 핵심 요약

- 반응형 레이아웃은 뷰포트 크기를 기준으로 스타일을 분기시킨다
- Grid나 Flex 기반 모두 media query와 함께 사용 가능
- 따라서, 즉 결과적으로 사용자 경험을 고려한 유연한 UI를 만들기 위해선 media query를 구조적으로 사용할 수밖에 없다