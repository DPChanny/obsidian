---
type: language
---
## 개요

Flexbox는 CSS에서 **1차원(가로 혹은 세로 한 방향)** 정렬을 위한 레이아웃 시스템이다.

기존 float, inline-block의 한계를 해결하고, 정렬, 간격, 정비율 분배 등을 더 직관적으로 처리할 수 있다.

Flex는 **두 개의 축** 개념을 바탕으로 동작한다:

- **Main axis (주축)**: 요소들이 나란히 배치되는 방향 (기본: 가로)
- **Cross axis (교차축)**: 주축과 수직인 방향 (기본: 세로)

---

## 기본 구조

```html
<div class="flex-container">
  <div class="box">1</div>
  <div class="box">2</div>
  <div class="box">3</div>
</div>

```

```css
.flex-container {
  display: flex;
  border: 1px solid #ccc;
  height: 150px;
}

.box {
  width: 60px;
  height: 60px;
  background: skyblue;
  margin: 5px;
}

```

---

## flex-direction – 주축 방향 지정

```css
.flex-container {
  display: flex;
  flex-direction: row; /* 기본값 */
}

```

|옵션|설명|
|---|---|
|`row`|왼쪽 → 오른쪽 (기본)|
|`row-reverse`|오른쪽 → 왼쪽|
|`column`|위 → 아래|
|`column-reverse`|아래 → 위|

---

## justify-content – 주축 정렬 방식

```css
.flex-container {
  display: flex;
  justify-content: center;
}

```

|값|설명|
|---|---|
|`flex-start`|주축 시작 지점에 정렬|
|`center`|가운데 정렬|
|`flex-end`|주축 끝 지점에 정렬|
|`space-between`|양 끝 고정, 사이 균등|
|`space-around`|양쪽 여백 포함 균등|
|`space-evenly`|완전히 균등한 간격|

---

## align-items – 교차축 정렬 (한 줄)

```css
.flex-container {
  display: flex;
  align-items: center;
}

```

|값|설명|
|---|---|
|`stretch`|교차축을 부모 높이에 맞게 늘림 (기본)|
|`flex-start`|위쪽 정렬|
|`center`|수직 가운데 정렬|
|`flex-end`|아래쪽 정렬|
|`baseline`|텍스트 기준선 정렬|

---

## align-self – 개별 아이템 교차축 정렬

개별 flex 아이템이 `align-items` 설정과 무관하게 정렬되도록 함.

```css
.box:nth-child(2) {
  align-self: flex-end;
}

```

```
→ 두 번째 박스만 아래쪽에 정렬됨

```

가능한 값은 `align-items`와 동일 (`flex-start`, `center`, `flex-end`, `stretch`, `baseline`)

---

## 예제 – 수직 중앙 정렬 카드 레이아웃

```html
<div class="card-container">
  <div class="card">카드</div>
</div>

```

```css
.card-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 300px;
  background: #f2f2f2;
}

.card {
  width: 200px;
  height: 100px;
  background: white;
  box-shadow: 0 0 5px rgba(0,0,0,0.2);
  display: flex;
  justify-content: center;
  align-items: center;
}

```

```
→ 카드가 부모 영역 정중앙에 배치됨
→ 내부 텍스트도 카드 안에서 정중앙에 위치

```

---

## 핵심 요약

- `flex`는 요소를 가로나 세로 한 줄로 정렬하는 데 특화되어 있다
- 주축 정렬은 `justify-content`, 교차축 정렬은 `align-items`
- 개별 요소는 `align-self`로 정렬 위치를 따로 줄 수 있다
- 따라서, 즉 결과적으로 가로 배치 + 정렬은 flex를 쓸 수밖에 없다