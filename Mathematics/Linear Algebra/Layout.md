---
related:
  - "[[Mathematics/Calculus/Jacobian Matrix|Jacobian Matrix]]"
  - "[[Mathematics/Linear Algebra/Scalar, Vector, Matrix|Scalar, Vector, Matrix]]"
---

행렬 미분 시 미분 결과의 차원(형태)을 결정하는 두 가지 주요 표기법(Layout)에 대한 정리임.

## 행렬 미분 표기법의 종류

행렬 미분에는 크게 분자 중심 표기법과 분모 중심 표기법이 존재하며, 어느 쪽을 기준으로 삼느냐에 따라 도출되는 그래디언트나 도함수의 차원(열 벡터인지 행 벡터인지)이 달라짐.

### 1. 분자 중심 표기법 (Numerator-layout)

- **개념**: 미분 결과의 형태가 분자(미분되는 함수)의 차원을 따름.
- **별칭**: Jacobian formulation
- **특징**: 스칼라(분자)를 열 벡터(분모)로 미분하면, 결과는 분자의 형태(스칼라의 차원 구조)에 맞춰 전치된 **행 벡터(Row Vector)** 가 됨.
- **활용**: 일반적인 수학적 관례와 다변수 미적분학에서 선형 근사나 연쇄 법칙(Chain Rule)을 표현할 때 주로 사용됨.

### 2. 분모 중심 표기법 (Denominator-layout)

- **개념**: 미분 결과의 형태가 분모(미분하는 변수)의 차원을 따름.
- **별칭**: Hessian formulation
- **특징**: 스칼라(분자)를 열 벡터(분모)로 미분하면, 결과는 분모와 동일한 형태인 **열 벡터(Column Vector)** 가 됨.
- **활용**: 기계학습(Machine Learning) 및 딥러닝 최적화 분야에서 압도적으로 많이 사용됨.

## 기계학습에서 분모 중심 표기법이 선호되는 이유

딥러닝을 비롯한 최적화 과정에서는 역전파(Backpropagation)를 통해 스칼라 값인 손실(Loss) 함수 $L$을 벡터 형태의 가중치 파라미터 $\mathbf{w}$로 미분하게 됨.

분모 중심 표기법을 적용하면, 산출된 그래디언트 $\nabla_{\mathbf{w}} L$ 가 미분 대상이었던 파라미터 $\mathbf{w}$ 와 **완전히 동일한 차원의 열 벡터**가 됨.

$$
\nabla_{\mathbf{w}} L = \frac{\partial L}{\partial \mathbf{w}} = \begin{bmatrix} \frac{\partial L}{\partial w_1} \\ \frac{\partial L}{\partial w_2} \\ \vdots \\ \frac{\partial L}{\partial w_n} \end{bmatrix} \in \mathbb{R}^n
$$

이러한 특성 덕분에 경사하강법(Gradient Descent) 수식에서 파라미터 업데이트를 다음과 같이 전치(Transpose) 기호 없이 직관적이고 간결하게 작성할 수 있음.

$$
 \mathbf{w} \leftarrow \mathbf{w} - \alpha \nabla_{\mathbf{w}} L 
$$

만약 분자 중심 표기법을 사용했다면 $\nabla_{\mathbf{w}} L$ 이 행 벡터가 되므로, 열 벡터인 $\mathbf{w}$ 와 차원을 맞추기 위해 매번 $(\nabla_{\mathbf{w}} L)^T$ 와 같이 전치 연산을 추가해야 하는 번거로움이 발생함.
