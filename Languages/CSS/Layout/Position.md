---
type: language
---
## 개요

`position` 속성은 요소의 배치 기준과 흐름에서의 역할을 정의한다.

기본 흐름을 유지할 것인지 벗어날 것인지, 어떤 기준으로 위치를 잡을 것인지에 따라

다양한 방식으로 요소를 제어할 수 있다.

---

## static (기본값)

문서의 흐름에 따라 자연스럽게 배치된다. 좌표값(`top`, `left`, 등)은 적용되지 않는다.

```css
.static-box {
  position: static;
}

```

```html
<div class="static-box">Static</div>

```

```
→ 기본적으로 위에서 아래로 쌓이며, 따로 위치 지정 불가

```

---

## relative – _살짝 위치만 조정하고 공간은 그대로_

요소가 원래 위치를 유지한 채 **렌더링 위치만 이동**한다.

다른 요소들과의 흐름은 유지되며, 해당 요소는 여전히 원래 자리의 공간을 차지한다.

### 기본 예시

```html
<div class="banner">
  <h1 class="title">첫 번째 타이틀</h1>
  <h2 class="subtitle">두 번째 부제목</h2>
</div>

```

```css
.banner {
  background: black;
  color: white;
  padding: 20px;
  height: 200px;
  position: relative;
}

.title {
  position: relative;
  top: 30px;
  left: 10px;
}

.subtitle {
  background: rgba(255,255,255,0.1);
  padding: 10px;
}

```

```
→ .title은 30px 아래, 10px 오른쪽으로 이동했지만
→ .subtitle은 .title의 원래 위치 기준으로 흐름상 바로 아래에 위치
→ 시각적으로는 간격이 커 보이지만 실제 레이아웃상 위치는 변하지 않음

```

### 겹침 발생 예시

```html
<div class="box one">One</div>
<div class="box two">Two</div>

```

```css
.box {
  width: 200px;
  height: 100px;
  background: lightgray;
  margin-bottom: 10px;
}

.one {
  position: relative;
  top: 40px;
  background: orange;
}

.two {
  background: lightblue;
}

```

```
→ .one은 시각적으로 아래로 내려갔지만
→ .two는 여전히 .one의 원래 공간 아래에 위치함
→ 결과적으로 시각적으로는 두 박스가 겹치게 됨

```

---

## absolute – _조상 요소 기준으로 위치 지정, 흐름에서 제외됨_

`absolute`는 **가장 가까운 `position`이 지정된 조상 요소**를 기준으로 위치를 계산한다.

그런 조상이 없으면 `<html>` 또는 `<body>` 기준이 되어 의도치 않게 전체 화면 기준으로 배치될 수 있다.

```html
<div class="card">
  <div class="badge">NEW</div>
  <p>상품 설명</p>
</div>

```

```css
.card {
  position: relative;
  width: 300px;
  height: 200px;
  border: 1px solid #ccc;
}

.badge {
  position: absolute;
  bottom: 10px;
  right: 10px;
  background: red;
  color: white;
  padding: 5px;
}

```

```
→ .badge는 .card 기준으로 오른쪽 아래에 위치됨
→ .card가 position: relative이므로, 기준 컨테이너 역할을 한다
→ 흐름에서 빠져 있어 .badge는 다른 요소와 겹칠 수 있다

```

### 왜 부모에 `relative`를 줘야 하나?

`absolute`는 위치 기준을 정할 조상이 필요하다.

그 기준이 되는 조상은 반드시 `position`이 있어야 한다 (`relative`, `absolute`, `fixed`, `sticky` 중 하나).

그렇지 않으면 **body 기준**으로 동작하게 된다.

### 부모에 position 없음

```css
.parent {
  /* position 없음 */
}
.child {
  position: absolute;
  top: 0;
  right: 0;
}

```

```
→ .child는 화면 전체 기준으로 오른쪽 위에 뜬다
→ 부모 영역 안에 안 있음. 예상한 결과 아님

```

### 부모에 `position: relative`

```css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 0;
  right: 0;
}

```

```
→ .child는 .parent 기준 오른쪽 위에 고정된다
→ 부모 내부에 잘 붙어있고, 레이아웃이 안정된다

```

### 결론

- `absolute`는 기준이 명확해야 의도한 위치에 붙는다
- 부모 요소에 `position: relative`를 지정해주면 **그 부모를 기준으로 정확하게 배치할 수 있다**
- 그래서 absolute 쓸 땐 → **부모 relative 습관화**는 필수
- 이는 `relative`, `absolute`, `fixed`, `sticky`중 `relative`가 가장 일반적이기 때문이다.

```
따라서, 즉 결과적으로 absolute는 단독으로 쓰면 제어가 어렵기 때문에
기준 부모를 명시하는 것이 사실상 필수적일 수밖에 없다.

```

---

## fixed – _뷰포트 기준으로 절대 고정_

브라우저의 화면(뷰포트)을 기준으로 위치가 고정된다.

스크롤해도 위치가 변하지 않으며 항상 고정되어 있다.

```html
<button class="chat-btn">💬 채팅</button>

```

```css
.chat-btn {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background: blue;
  color: white;
  padding: 10px 20px;
}

```

```
→ 화면 오른쪽 아래에 고정
→ 페이지를 스크롤해도 항상 같은 위치에 남아있음

```

---

## sticky – _조건부 고정 (스크롤 기준)_

스크롤이 지정 위치에 도달하면 `fixed`처럼 고정된다.

그 전까지는 `relative`처럼 동작한다.

```html
<section>
  <h2 class="sticky-head">제품 카테고리</h2>
  <ul>...</ul>
</section>

```

```css
.sticky-head {
  position: sticky;
  top: 0;
  background: white;
  padding: 10px;
}

```

```
→ 스크롤 내리다 상단에 도달하면 고정됨
→ 부모 요소를 벗어나면 다시 흐름에 따라 스크롤됨

```

---

## 핵심 요약

|position|기준 위치|흐름 유지|공간 차지|주요 용도|
|---|---|---|---|---|
|static|없음|유지|O|기본 배치|
|relative|자기 위치|유지|O|살짝 이동|
|absolute|조상 기준|이탈|X|UI 겹침|
|fixed|뷰포트 기준|이탈|X|고정 버튼|
|sticky|스크롤 기준|조건부 유지|O|섹션 고정|

---

## 주의

- `relative`는 위치 조정용이지 배치용이 아니다. 많이 이동하면 시각 겹침 발생 → absolute 고려
- `absolute`는 부모 기준 위치다. 기준 부모에 `position: relative`를 반드시 줘야 예상대로 동작함
- `fixed`는 항상 화면 기준 → 어떤 부모에도 영향 받지 않음
- `sticky`는 스크롤 고정용으로 가장 자주 쓰이는 패턴 중 하나지만, 조건부라 예외 상황 주의해야 함

```
따라서, 즉 결과적으로 레이아웃을 제어하려면
position의 "기준점"과 "공간 점유 여부"를 정확히 이해할 수밖에 없다.

```