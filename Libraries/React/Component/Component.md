---
type: library
---
컴포넌트는 함수(function component) 또는 클래스(class component)로 작성할 수 있으며, React의 렌더링 타임(runtime)은 이 컴포넌트들이 반환하는 JSX를 기반으로 Virtual DOM을 생성하고, 실제 DOM에 반영한다. 이때 **함수형 컴포넌트는 `props`라는 인자를 받아, 그 값에 따라 JSX를 반환하는 순수 함수처럼 작동**한다. 렌더링은 상태(state)나 props가 변경되었을 때 다시 수행되며, 그 결과 UI가 최신 상태로 갱신된다.

### 왜 필요한가?

컴포넌트가 필요한 이유는 다음과 같다

- UI를 기능 단위로 분리하면 코드가 이해하기 쉬워진다
- 재사용성을 높이고, 중복을 줄일 수 있다
- 상태 관리를 지역화할 수 있어 복잡성을 낮춘다
- 전체 애플리케이션을 트리 구조로 분해해 확장 가능성이 높아진다

### React는 왜 이 방식을 채택했는가?

React는 초기부터 UI를 데이터에 따라 선언적으로 표현해야 한다는 관점에서 출발했다. 즉, "어떻게 그릴지"가 아니라 "어떤 상태일 때 어떤 모양이냐"를 기준으로 UI를 구성하려 한 것이다. 이를 실현하기 위한 구조가 바로 컴포넌트이다.

컴포넌트는 렌더링 타임에 JSX를 반환하며, 이 결과를 바탕으로 가상 DOM이 구성된다. React는 이를 통해 변경된 부분만 실제 DOM에 반영하는 고성능 업데이트가 가능하도록 설계되었다. 렌더링 단위가 작고 독립적일수록 업데이트 비용은 줄고, 예측 가능성은 높아진다. 상태나 props가 바뀔 때마다 컴포넌트 함수가 다시 실행되며, 새롭게 JSX를 반환하는 구조를 갖는다.

```tsx
import React, { useState } from "react";

const ClickButton: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  const handleClick = (): void => {
    setCount(prev => prev + 1);
  };

  return (
    <button onClick={handleClick}>
      클릭 횟수: {count}
    </button>
  );
};

export default ClickButton;
```