---
type: library
parent: "[[Libraries/React|React]]"
---
## 개념

**Fiber는 React 렌더링의 핵심 구조다.**

2017년 React 16에서 도입된 이 구조는 기존 Virtual DOM 기반 재귀적 처리 방식에서 탈피하여, **작업을 쪼개고 중단하고 재개하며 우선순위를 조절할 수 있는 일종의 작업 엔진**으로 작동한다.

Fiber는 컴포넌트를 단순한 트리로 보는 대신, 각 노드를 `Fiber Node`라는 구조화된 단위로 변환하여, 이들을 하나의 작업(work) 단위로 처리한다. 결국 이 구조 덕분에 React는:

- 렌더링을 **스케줄링**할 수 있고
- 처리 도중 **중단과 재개**가 가능하며
- 사용자 입력 등 긴급한 작업에 **우선순위를 줄 수**밖에 없다.

---

## 왜 Fiber가 필요한가

### 기존 Virtual DOM의 한계

- 재귀 기반으로 렌더링 → 큰 컴포넌트 트리에서 렌더링을 중단할 수 없음
- 모든 변경을 한 번에 계산 → 사용자 인터랙션이 밀림
- 트리 탐색 중에는 JS 스레드가 점유되어 **메인 스레드 블로킹**

### Fiber가 해결한 방식

- 각 컴포넌트를 **작은 단위(Fiber Node)**로 분할
- 이 노드들을 순차적으로 처리하며, 중간에 **pause-resume 가능**
- 이를 통해 React는 **시간 분할 렌더링(time slicing)**과 **선택적 우선순위 처리**를 지원하게 된다

---

## 구조: Fiber Node란 무엇인가

각 컴포넌트는 하나의 **Fiber Node**로 표현된다. 이 노드는 다음과 같은 정보들을 가진다:

- `type`: 어떤 컴포넌트인지 (function, class, DOM 등)
- `props`: 현재 props 정보
- `stateNode`: 실제 DOM 또는 class instance
- `return`, `child`, `sibling`: 트리 탐색을 위한 연결 정보
- `alternate`: 이전 렌더링에서의 Fiber (이전 버전)
- `flags`: 어떤 작업이 필요한지 표시 (삽입, 삭제 등)

---

## Fiber가 이끄는 렌더링 파이프라인

Fiber 구조는 React 렌더링을 다음과 같은 **3단계로 분리**해 처리하게 만든다:

1. **Render Phase (Work Phase)**
    
    변경을 계산하고 어떤 작업이 필요한지 판단
    
2. **Complete Phase**
    
    DOM 변경을 위한 구체적인 명령 목록(Effect List) 작성
    
3. **Commit Phase**
    
    실제 DOM에 반영 + lifecycle hook 실행
    

이 구조 덕분에 React는 **렌더링 성능과 사용자 경험을 모두 지키는 방향**으로 진화할 수밖에 없었다.

---

## 결론

Fiber는 단순한 내부 최적화가 아니다. **React가 "비동기적이고 유연한 UI 렌더링 엔진"으로 작동하게 만든 핵심 아키텍처**다. 이를 통해 React는 중단 가능한 렌더링, 우선순위 기반 업데이트, 그리고 향후의 Concurrent Feature까지 확장할 수 있게 되었다.

결국 Fiber를 이해하지 않고 React 렌더링을 이해할 수는 없다.