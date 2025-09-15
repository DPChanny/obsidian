---
type: library
---
## 1. queryKey란 무엇인가?

React Query는 데이터를 `queryKey`를 기준으로 캐시한다.

queryKey는 캐시의 **주소이자 식별자이며, 동시에 구독 대상이 되는 기준값**이다.

```tsx
useQuery({ queryKey: ['posts'], queryFn: fetchPosts })

```

- `'posts'`라는 캐시에 결과가 저장됨
- 이후 동일한 키로 요청하면 캐시를 먼저 사용

---

## 2. 왜 배열 구조인가?

queryKey는 **배열로 작성하는 것이 표준**이다.

```tsx
['post', id]
['user', { filter: 'active' }]

```

- 배열의 첫 요소는 리소스 종류
- 이후 요소들은 **상세 구분값, 파라미터, 조건** 등을 추가하는 용도
- 내부적으로 JSON.stringify로 직렬화되어 고유한 해시가 생성됨

---

## 3. 목록 vs 상세 구분

```tsx
['post']             // 전체 목록
['post', 1]          // 특정 ID
['post', { tag: 'dev' }]  // 필터 조건

['user']             // 유저 목록
['user', id]         // 개별 유저 상세

```

→ 이처럼 **queryKey 설계는 캐시 범위를 명확히 구분하는 기준**이 된다.

---

## 4. 조건/필터 포함 방식

조건이 있는 목록은 객체를 포함시켜 구분한다:

```tsx
['posts', { category: 'tech', sort: 'latest' }]

```

- filter, 검색어, 정렬 기준이 다르면 다른 캐시로 간주됨
- 동일한 fetch 함수라도 key가 다르면 따로 저장됨

---

## 5. invalidateQueries와 queryKey의 관계

React Query는 `invalidateQueries({ queryKey })` 호출 시,

**queryKey가 그 값으로 시작되는 모든 쿼리**를 무효화한다.

```tsx
invalidateQueries({ queryKey: ['post'] })
// → ['post'], ['post', 1], ['post', { filter: ... }] 등 모두 무효화됨

invalidateQueries({ queryKey: ['post', 1] })
// → 정확히 ['post', 1]만 무효화됨

```

- 내부적으로는 prefix match (`startsWith`)로 처리됨
- 따라서 **queryKey는 계층 구조처럼 활용할 수밖에 없다**

---

## 6. 실전 예시

### 예시 1 – 전체 포스트 목록 무효화

```tsx
queryClient.invalidateQueries({ queryKey: ['post'] })

```

→ 목록, 상세, 필터 포함 전부 리프레시됨

---

### 예시 2 – 특정 포스트만 다시 불러오기

```tsx
queryClient.invalidateQueries({ queryKey: ['post', 42] })

```

→ ID 42번 포스트만 refetch

---

## 7. 정리

|항목|설명|
|---|---|
|queryKey|캐시의 주소이자 식별자|
|배열 구조|리소스 + 세부 조건 구분|
|목록 vs 상세|['post'] vs ['post', id]|
|invalidate 방식|prefix match 구조|
|조건 포함 방식|객체를 포함하여 변형된 캐시 생성 가능|

queryKey 설계는 단순한 키 지정이 아니라

**React Query 전체 상태 구조와 캐시 전략을 정의하는 핵심 요소**다.

정확하고 계층적인 queryKey 구조 없이는 상태 일관성도, 성능도 확보할 수 없다.