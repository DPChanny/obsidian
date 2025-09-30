---
---
## 박스 구성 요소

모든 HTML 요소는 사각형 박스 형태로 렌더링되며, 이 박스는 네 가지 영역으로 구성된다:

- **Content**: 실제 콘텐츠(텍스트, 이미지 등)가 들어가는 영역
- **Padding**: 콘텐츠와 테두리 사이의 내부 여백
- **Border**: 요소를 감싸는 선
- **Margin**: 요소 바깥의 여백

```css
.box {
  margin: 10px;
  border: 5px solid black;
  padding: 15px;
  width: 100px;
}

```

```html
<div class="box">Box</div>

```

```
→ 총 너비는 10px(왼쪽 마진) + 5px(왼쪽 보더) + 15px(왼쪽 패딩) + 100px(콘텐츠) + 오른쪽도 동일 = 160px

```

---

## `box-sizing`: content-box vs border-box

기본값은 `content-box`이며, 이 경우 `width`는 콘텐츠 영역만을 의미한다. 반면 `border-box`를 사용하면 `width`에 padding과 border까지 포함되어 계산된다.

```css
/* 기본 (content-box) */
.box1 {
  width: 100px;
  padding: 10px;
  border: 5px solid black;
  box-sizing: content-box;
}

/* border-box 사용 */
.box2 {
  width: 100px;
  padding: 10px;
  border: 5px solid black;
  box-sizing: border-box;
}

```

```
.box1 → 실제 렌더 너비 = 100 + 10*2 + 5*2 = 130px
.box2 → 실제 렌더 너비 = 100px 고정 (padding, border 포함됨)

```

---

## Margin Collapse (마진 병합)

상하 방향의 margin이 겹칠 경우, 더 큰 값 하나만 적용된다. 좌우 방향에는 적용되지 않는다.

```html
<div class="box-a"></div>
<div class="box-b"></div>

```

```css
.box-a {
  margin-bottom: 30px;
  height: 50px;
  background: lightblue;
}

.box-b {
  margin-top: 20px;
  height: 50px;
  background: lightcoral;
}

```

```
→ 두 박스 사이 여백은 50px이 아닌 30px 하나만 적용된다.

```

---

## 시각 실습 예제

```html
<div class="box-demo">Box Demo</div>

```

```css
.box-demo {
  margin: 20px;
  border: 5px solid black;
  padding: 15px;
  background-color: lightgray;
  box-sizing: border-box;
}

```

```
→ 배경색으로 내부 여백(padding)을 확인하고, border로 테두리를 확인하며,
→ margin은 요소 바깥 여백을 확인하는 시각 실습이 된다.

```

---

## 핵심 요약

- 모든 요소는 콘텐츠, 패딩, 테두리, 마진으로 이루어진 사각형 박스다.
- `box-sizing`을 잘못 사용하면 레이아웃이 예기치 않게 깨질 수밖에 없다.
- margin collapse는 상하 마진 병합을 통해 의도치 않은 여백 문제를 유발할 수 있으므로 주의해야 한다.