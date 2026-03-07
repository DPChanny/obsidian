---
---
## 기본 선택자

가장 단순한 형태로, 요소를 이름, 클래스, 아이디로 선택한다.

```css
/* 태그 선택자 */
p {
  color: red;
}

/* 클래스 선택자 */
.button {
  background-color: blue;
}

/* 아이디 선택자 */
#main-title {
  font-size: 2rem;
}

```

```html
<p>Hello</p>
<div class="button">Click me</div>
<h1 id="main-title">Welcome</h1>

```

각각 `p`, `class="button"`, `id="main-title"` 요소에 스타일이 적용된다.

---

### 클래스 조합 선택자

하나의 요소에 **여러 클래스가 동시에 붙은 경우**에만 적용된다.

```css
.button.danger {
  background-color: red;
}

```

```html
<button class="button">일반</button>
<button class="button danger">위험</button>

```

`.button.danger`는 두 클래스를 동시에 가진 요소만 선택된다.

공백 없이 붙이면 "AND 조건", 공백이 있으면 "자식 선택자"가 된다.

---

## 조합 선택자

선택자 간의 관계를 통해 더 정밀한 요소 선택이 가능하다.

---

### 후손 선택자 (`A B`)

`A`의 모든 **하위 후손** 중 `B` 요소를 선택한다. 중첩되어 있어도 포함된다.

```css
div p {
  color: green;
}

```

```html
<div>
  <p>적용됨</p>
  <section>
    <p>이것도 적용됨</p>
  </section>
</div>

```

→ `div` 내부의 모든 `p`에 적용됨

---

### 자식 선택자 (`A > B`)

`A`의 **직계 자식** 중 `B` 요소만 선택한다.

```css
ul > li {
  list-style: none;
}

```

```html
<ul>
  <li>적용됨</li>
  <section>
    <li>적용 안 됨</li>
  </section>
</ul>

```

→ 첫 번째 `li`만 적용되고, 중첩된 `li`는 제외된다

---

### 비교 요약

|선택자|의미|적용 범위|
|---|---|---|
|`A B`|A의 후손 B|하위 전체|
|`A > B`|A의 자식 B|한 단계 아래만|

---

### 상태 선택자

마우스나 키보드 등 사용자 인터랙션 상태에 따라 스타일을 적용한다.

```css
a:hover {
  color: orange;
}

input:focus {
  border-color: skyblue;
}

```

```html
<a href="#">Link</a>
<input type="text" />

```

링크를 hover하거나, input을 focus할 때 각각 스타일이 적용된다.

---

## 속성 선택자

HTML 속성 조건을 기반으로 요소를 선택한다.

```css
input[type="text"] {
  border: 1px solid gray;
}

```

```html
<input type="text" />
<input type="password" />

```

`type="text"`인 input만 선택되어 스타일이 적용된다.

---

## 우선순위와 결합 규칙 (Specificity)

선택자 간 충돌이 발생할 때는 **우선순위(Specificity)**에 따라 스타일이 결정된다.

|선택자 유형|점수|
|---|---|
|인라인 스타일 (`style=""`)|1000|
|아이디 선택자 (`#id`)|100|
|클래스 / 속성 / 상태 (`.class`, `[type]`, `:hover`)|10|
|태그 (`div`, `p`)|1|

```css
p {
  color: black;         /* 1 */
}

.content p {
  color: green;         /* 10 + 1 = 11 */
}

#main .content p {
  color: red;           /* 100 + 10 + 1 = 111 */
}

```

```html
<div id="main">
  <div class="content">
    <p>Hello</p>
  </div>
</div>

```

→ 최종적으로 `p`는 빨간색(red)이 적용됨 (우선순위가 가장 높기 때문)

---

## 핵심 요약

- 선택자는 HTML 요소를 선택해 스타일을 적용하는 기본 구조이다.
- 다양한 관계(자식, 후손), 상태, 속성 조합을 통해 정밀한 지정이 가능하다.
- 여러 선택자가 겹칠 땐 **우선순위**를 고려해서 원하는 스타일이 제대로 적용되도록 해야 한다.