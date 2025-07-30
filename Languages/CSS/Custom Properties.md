---
type: language
parent: "[[Languages/CSS|CSS]]"
---
## 개요

CSS 변수는 `--이름` 형식으로 선언하고, `var(--이름)`으로 사용하는 사용자 정의 속성이다.

정식 명칭은 **Custom Properties**며, 반복되는 값이나 테마를 **중앙에서 제어**할 수 있게 한다.

---

## 기본 문법

```css
:root {
  --primary-color: #4a90e2;
  --font-size-base: 16px;
}

```

```css
.button {
  background: var(--primary-color);
  font-size: var(--font-size-base);
}

```

```
→ :root는 전역 변수 선언 영역 (html 요소 기준)
→ var() 함수로 변수 참조 가능

```

---

## 변수의 상속

- CSS 변수는 **상속된다**
- 부모 요소에 선언하면 자식 요소에서 자동 사용 가능

```css
.card {
  --card-bg: white;
}
.card-content {
  background: var(--card-bg);
}

```

---

## fallback (대체값) 사용

```css
.button {
  color: var(--text-color, black);
}

```

→ `--text-color`가 없으면 `black` 사용

---

## 실전 예제: 테마 전환 (다크모드 대응)

```css
:root {
  --bg: white;
  --fg: black;
}
@media (prefers-color-scheme: dark) {
  :root {
    --bg: #111;
    --fg: #eee;
  }
}
body {
  background: var(--bg);
  color: var(--fg);
}

```

```
→ 시스템 설정에 따라 자동으로 다크/라이트 전환됨

```

---

## 실전 예제: 컴포넌트 내부 커스터마이징

```css
.button {
  --btn-color: #222;
  color: var(--btn-color);
}
.button.danger {
  --btn-color: red;
}

```

→ `.danger` 클래스가 있으면 버튼 색이 red로 오버라이드됨

---

## 변수 vs SCSS 변수 차이

|항목|CSS 변수|SCSS 변수|
|---|---|---|
|사용 시점|**런타임**|컴파일 타임|
|동적 제어|가능 (JS 연동 가능)|불가능|
|상속|됨|안 됨|
|실제 DOM에 노출|O|X|

```
→ CSS 변수는 실행 중에도 바꿀 수 있음 (예: JS로 테마 전환)

```

---

## 핵심 요약

- CSS 변수는 `--이름`으로 선언하고 `var(--이름)`으로 사용
- 중복 스타일, 테마, 유지보수, 반응형 등에서 강력함
- 따라서, 즉 결과적으로 **스타일을 체계적으로 관리하려면 CSS 변수 기반 설계를 쓸 수밖에 없다**