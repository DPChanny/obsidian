---
type: language
parent: "[[Languages/CSS/Layout|Layout]]"
---
## 개요

CSS Grid는 **가로 + 세로 모두를 동시에 배치할 수 있는** 강력한 레이아웃 도구다.

Flexbox가 한 줄 정렬이라면, Grid는 **행과 열 모두를 직접 정의하고 제어**할 수 있다.

```css
.grid-container {
  display: grid;
}

```

---

## grid-template-columns / rows – 행과 열 구성

```css
.grid-container {
  display: grid;
  grid-template-columns: 100px 1fr 2fr;
  grid-template-rows: 50px 100px;
}

```

```
→ 열: 첫 열 100px, 둘째는 1비율, 셋째는 2비율
→ 행: 첫 행 50px, 둘째 행 100px

```

---

## gap – 셀 사이 간격 설정

```css
.grid-container {
  display: grid;
  gap: 10px;
}

```

|속성|설명|
|---|---|
|`gap`|행+열 모두|
|`row-gap`|행 사이 간격|
|`column-gap`|열 사이 간격|

---

## repeat(), fr 단위 – 유연한 레이아웃

```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

```

```
→ 1fr 1fr 1fr과 같음
→ 동일한 비율로 3등분

```

---

## grid-column / grid-row – 셀 병합

```css
.item1 {
  grid-column: 1 / 3;
}
.item2 {
  grid-row: 1 / 3;
}

```

```
→ item1은 1~3번 열까지 가로 병합 (2칸)
→ item2는 1~3번 행까지 세로 병합 (2칸)

```

반응형 설계에서 병목을 유발하므로 꼭 필요하지 않다면 사용하지 않는다.

---

## grid-template-areas – 시멘틱한 영역 정의

```css
.grid {
  display: grid;
  grid-template-columns: 150px 1fr;
  grid-template-areas:
    "header header"
    "sidebar content"
    "footer footer";
}
.grid > .header { grid-area: header; }
.grid > .sidebar { grid-area: sidebar; }
.grid > .content { grid-area: content; }
.grid > .footer { grid-area: footer; }

```

```html
<div class="grid">
  <div class="header">Header</div>
  <div class="sidebar">Sidebar</div>
  <div class="content">Content</div>
  <div class="footer">Footer</div>
</div>

```

```
→ 영역별 이름으로 직관적으로 레이아웃 구성 가능

```

---

## 핵심 요약

- Grid는 2차원 레이아웃 (행 + 열 동시 배치)에 특화
- `grid-template-columns` / `rows`로 직접 격자 구성
- `grid-area`로 시멘틱한 배치 가능
- Flex보다 정교한 레이아웃이 필요할 때는 Grid를 쓸 수밖에 없다