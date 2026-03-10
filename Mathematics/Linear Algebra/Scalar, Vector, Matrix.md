---
related:
  - "[[Mathematics/Calculus/Jacobian Matrix|Jacobian Matrix]]"
  - "[[Mathematics/Linear Algebra/Layout|Layout]]"
---

선형대수학에서 데이터를 다루는 기본 구조와 수학적 표기법에 대한 정리임.

## 데이터 구조의 차원적 분류

수학과 컴퓨터 과학(특히 기계학습)에서 다루는 데이터는 텐서(Tensor)라는 다차원 배열 구조로 일반화됨.

1. **스칼라 (Scalar)**
   - **개념**: 단일 실숫값. 방향성이 없는 크기(Magnitude)만을 가짐. ($0$차원 텐서)
   - **표기법**: 소문자 이탤릭체.
   - **수식**: $x \in \mathbb{R}$

2. **벡터 (Vector)**
   - **개념**: 스칼라가 $1$차원 배열 형태로 나열된 데이터 구조. ($1$차원 텐서)
   - **표기법**: 소문자 볼드체. 수학적 관례상 별도의 언급이 없는 한 **열 벡터(Column Vector)** 를 기본으로 간주함.
   - **수식**:

$$
\mathbf{x} = \begin{bmatrix} x_1 \\ x_2 \\ \vdots \\ x_n \end{bmatrix} \in \mathbb{R}^n
$$

1. **행렬 (Matrix)**
   - **개념**: 스칼라가 행(Row)과 열(Column)의 $2$차원 배열 형태로 나열된 데이터 구조. ($2$차원 텐서)
   - **표기법**: 대문자 볼드체.
   - **수식**:

$$
\mathbf{X} = \begin{bmatrix} x_{11} & x_{12} & \cdots & x_{1n} \\ x_{21} & x_{22} & \cdots & x_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ x_{m1} & x_{m2} & \cdots & x_{mn} \end{bmatrix} \in \mathbb{R}^{m \times n}
$$

1. **텐서 (Tensor)**
   - **개념**: 스칼라, 벡터, 행렬을 포괄하여 $n$차원으로 일반화한 다차원 데이터 배열.

## 열 벡터(Column Vector) 기본 표기 관례

수학 및 기계학습 분야에서는 벡터를 표기할 때 기본적으로 가로가 아닌 세로로 나열된 열 벡터 형식을 사용함. 이는 다음과 같은 세 가지 주요 이유에 기인함.

### 1. 선형 변환과 행렬 곱셈 관례

선형대수학에서 행렬 $\mathbf{A}$가 벡터 $\mathbf{x}$에 작용하는 선형 변환은 표준적으로 $\mathbf{A}\mathbf{x}$ 구조로 표기함.

행렬 곱셈 규칙(앞 행렬의 열 개수 = 뒤 행렬의 행 개수)이 성립하려면 입력되는 벡터 $\mathbf{x}$는 필연적으로 열 벡터여야 함. 만약 $\mathbf{x}$를 행 벡터로 가정한다면, 연산 순서가 $\mathbf{x}\mathbf{A}$로 뒤바뀌어 오랜 기간 정립된 표기 관례와 충돌하게 됨.

### 2. 연립방정식의 직관적 표현

선형 연립방정식을 행렬 방정식 $\mathbf{A}\mathbf{x} = \mathbf{b}$ 로 나타낼 때, 미지수 집합인 $\mathbf{x}$와 상수 집합인 $\mathbf{b}$를 위에서 아래로 읽히는 열 벡터로 배치하는 것이 수식을 전개하고 시각적으로 이해하는 데 가장 직관적임.

### 3. 분모 중심 표기법에서의 업데이트 편의성

머신러닝 최적화 과정에서 스칼라 목적 함수를 벡터 가중치 파라미터 $\mathbf{w}$로 미분할 때, 분모 중심 표기법(Denominator-layout)을 따르면 산출된 그래디언트 $\nabla_{\mathbf{w}} L$ 가 원래의 파라미터 벡터 $\mathbf{w}$ 와 동일한 형태의 열 벡터가 됨.

이로 인해 경사하강법 등에서 $\mathbf{w} \leftarrow \mathbf{w} - \alpha \nabla_{\mathbf{w}} L$ 와 같이 별도의 전치(Transpose) 연산 없이 변수 차원 그대로 업데이트 수식을 직관적으로 작성할 수 있음.
