---
type: language
---
## `display` 속성

요소의 기본 배치 방식을 결정하는 가장 기본적인 속성이다.

```css
.block {
  display: block;
}
.inline {
  display: inline;
}
.inline-block {
  display: inline-block;
}
.hidden {
  display: none;
}

```

```
block → 한 줄 전체 차지, 줄 바꿈 발생 (div 등)
inline → 내용만큼 차지, 줄 바꿈 없음, width/height 적용 불가 (span 등)
inline-block → inline처럼 배치 + 사이즈 지정 가능
none → 요소를 렌더링하지 않음 (화면에서 제거)

```