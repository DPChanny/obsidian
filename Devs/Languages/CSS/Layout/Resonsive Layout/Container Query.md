---
---
## 개요

기존의 `@media`는 브라우저 너비(뷰포트)를 기준으로 반응형 스타일을 적용한다.

하지만 **재사용 가능한 UI 컴포넌트**의 경우, 화면 전체가 아니라

**부모 요소의 크기**에 따라 스타일이 바뀌어야 한다.

→ 이럴 때 사용하는 것이 `@container`, 즉 **Container Query**다.

→ "내가 속한 박스가 얼마나 넓은지에 따라 스타일이 바뀐다."

---

## 핵심 개념: container-type vs display

|속성|역할|작동 대상|
|---|---|---|
|`display`|자식을 어떻게 배치할지 정의 (block, flex 등)|**레이아웃 방식**|
|`container-type`|이 요소를 container query의 기준으로 쓸 수 있는지 설정|**조건 판단 기준**|

```
→ display는 '레이아웃 구조', container-type은 '스타일 조건 기준'
→ 역할이 완전히 다르다. 같이 써야 한다.

```

---

## container 관련 속성 정리

### 1. `container-type`

이 요소를 container로 만들 것인지, 어떤 기준으로 판단할지를 정의

```css
.container {
  container-type: inline-size;
}

```

|값|설명|
|---|---|
|`none` (기본값)|컨테이너 아님|
|`inline-size`|너비 기준 컨테이너|
|`size`|너비 + 높이 모두 기준|

---

### 2. `container-name`

여러 컨테이너 중 하나를 선택할 수 있도록 이름을 지정

```css
.container {
  container-name: card;
}

```

---

### 3. shorthand: `container`

```css
.container {
  container: card / inline-size;
}

```

→ container-name + container-type을 한 줄로 지정

---

## 기본 사용 예제

```html
<div class="card-wrapper">
  <div class="card">컨테이너 안의 카드</div>
</div>

```

```css
.card-wrapper {
  container: card / inline-size;
  width: 100%;
  max-width: 600px;
  border: 1px solid #aaa;
  padding: 16px;
}

.card {
  font-size: 1rem;
  padding: 20px;
  background: #f0f0f0;
}

@container card (min-width: 400px) {
  .card {
    font-size: 1.5rem;
    background: #e0f7ff;
  }
}

```

```
→ .card-wrapper가 400px 이상일 때만 .card 스타일이 달라짐
→ 뷰포트와 무관. 부모 요소 크기 기준.

```

---

## 언제 써야 하나?

- 카드, 위젯, 컴포넌트처럼 **모듈 단위 UI**일 때
- 페이지마다 들어가는 **위치와 크기가 달라지는 경우**
- 미디어쿼리로는 대응이 불가능하거나 과한 경우

---

## 핵심 요약

- `container-type`은 스타일 조건 판단 기준
- `display`는 자식 배치용 → 함께 써야 완성
- container query는 뷰포트 기준이 아닌 **부모 기준 반응형**
- 따라서, 즉 결과적으로 **모듈 기반 반응형 UI를 만들기 위해선 container-query를 쓸 수밖에 없다**