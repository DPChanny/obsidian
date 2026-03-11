---
tags: []
related:
  - "[[Tensor Calculus|Tensor Calculus]]"
  - "[[Mathematics/Linear Algebra/Layout|Layout]]"
---

스칼라 미분과 벡터 미분을 하나의 일관된 형태로 통합하는 다변수 미분의 일반화된 행렬인 야코비안(Jacobian) 행렬에 대한 정리임.

## 야코비안 행렬의 일반화

스칼라는 단지 크기가 1인 특수한 형태의 벡터($m=1$)이므로, 스칼라 함수와 벡터 함수의 미분은 본질적으로 다변수 함수의 선형 근사인 **야코비안 행렬(Jacobian Matrix)** 하나로 완벽하게 일반화됨.

### 함수의 차원에 따른 미분 결과 분류

함수 $\mathbf{f}: \mathbb{R}^n \rightarrow \mathbb{R}^m$ 에서 입력 변수의 차원 $n$과 출력 함수의 차원 $m$에 따라 미분 결과의 형태가 결정됨.

(※ 아래는 모두 '분자 중심 표기법(Numerator-layout)'을 기준으로 함)

1. **스칼라 $\rightarrow$ 스칼라 함수** ($n=1, m=1$)
   - 도함수는 $1 \times 1$ 크기의 스칼라.
2. **벡터 $\rightarrow$ 스칼라 함수** ($n>1, m=1$)
   - 도함수는 $1 \times n$ 크기의 행 벡터 (그래디언트의 전치, $\nabla_{\mathbf{x}} f^T$).
3. **스칼라 $\rightarrow$ 벡터 함수** ($n=1, m>1$)
   - 도함수는 $m \times 1$ 크기의 열 벡터.
4. **벡터 $\rightarrow$ 벡터 함수** ($n>1, m>1$)
   - 도함수는 $m \times n$ 크기의 행렬. 이를 **야코비안 행렬(Jacobian Matrix)** 이라 부름.
   - 특수한 경우로 입력과 출력의 차원이 같을 때($m=n$), 야코비안은 $n \times n$ 정방행렬이 됨.

## 야코비안 행렬의 정의와 당위성

벡터 함수 $\mathbf{f}(\mathbf{x}) \in \mathbb{R}^m$을 벡터 $\mathbf{x} \in \mathbb{R}^n$로 미분할 때, 수학적 관례인 분자 중심 표기법에 따른 야코비안 행렬 $\mathbf{J} \in \mathbb{R}^{m \times n}$은 다음과 같이 정의됨.

$$
\mathbf{J} = \frac{\partial \mathbf{f}}{\partial \mathbf{x}^T} = \begin{bmatrix} \frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} & \cdots & \frac{\partial f_1}{\partial x_n} \\ \frac{\partial f_2}{\partial x_1} & \frac{\partial f_2}{\partial x_2} & \cdots & \frac{\partial f_2}{\partial x_n} \\ \vdots & \vdots & \ddots & \vdots \\ \frac{\partial f_m}{\partial x_1} & \frac{\partial f_m}{\partial x_2} & \cdots & \frac{\partial f_m}{\partial x_n} \end{bmatrix}
$$

### 왜 야코비안 행렬은 $m \times n$ (출력 $\times$ 입력) 형태인가?

수학에서 야코비안 행렬을 위와 같이 (함수의 개수 $m$ $\times$ 변수의 개수 $n$) 형태인 분자 중심 표기법으로 정의한 근본적인 이유는 국소적 선형 근사와 연쇄 법칙을 선형대수학의 **행렬 곱셈**으로 자연스럽게 표현하기 위함임.

1. **국소적 선형 근사 (Local Linear Approximation)**
   다변수 벡터 함수 $\mathbf{f}(\mathbf{x})$의 미소 변화량 $\Delta \mathbf{f} \in \mathbb{R}^m$ 은 입력의 미소 변화량 $\Delta \mathbf{x} \in \mathbb{R}^n$ 에 대하여 다음과 같이 1차 테일러 근사됨.

$$
 \mathbf{f}(\mathbf{x} + \Delta \mathbf{x}) \approx \mathbf{f}(\mathbf{x}) + \mathbf{J}\Delta \mathbf{x}
$$

   $\Delta \mathbf{x}$ 는 $n \times 1$ 열 벡터이고 연산의 결과인 $\Delta \mathbf{f}$ 도 $m \times 1$ 열 벡터여야 함.

   행렬 곱셈 규칙이 성립하여 $m \times 1$ 의 결과를 도출하려면, 앞에 곱해지는 선형 변환 행렬인 $\mathbf{J}$는 반드시 $m \times n$ 크기의 행렬이어야만 함.

1. **연쇄 법칙 (Chain Rule)의 직관적 확장**
   합성 함수 $\mathbf{z} = \mathbf{f}(\mathbf{g}(\mathbf{x}))$ 를 미분할 때, 야코비안 행렬($m \times n$)을 사용하면 다변수 함수의 연쇄 법칙을 1변수 미분과 동일한 직관적인 형태의 행렬 곱셈으로 전개할 수 있음.

$$
 \frac{\partial \mathbf{z}}{\partial \mathbf{x}} = \frac{\partial \mathbf{z}}{\partial \mathbf{y}} \frac{\partial \mathbf{y}}{\partial \mathbf{x}} = \mathbf{J}_f \mathbf{J}_g
$$

## 야코비안 계산 예시 (3차원 $\rightarrow$ 2차원)

입력 변수가 3차원($n=3$)이고 출력 함수가 2차원($m=2$)인 벡터 함수 $\mathbf{f}: \mathbb{R}^3 \rightarrow \mathbb{R}^2$ 의 미분 예시.

입력 벡터 $\mathbf{x} = \begin{bmatrix} x \\ y \\ z \end{bmatrix}$ 에 대하여 함수 $\mathbf{f}(\mathbf{x})$ 가 아래와 같이 주어졌다고 가정함.

$$
\mathbf{f}(x, y, z) = \begin{bmatrix} f_1(x, y, z) \\ f_2(x, y, z) \end{bmatrix} = \begin{bmatrix} x^2 y \\ x + z^3 \end{bmatrix}
$$

이때의 야코비안 행렬 $\mathbf{J}$ 는 $2 \times 3$ ($m \times n$) 크기를 가지며, 각 행은 개별 스칼라 함수($f_1, f_2$)의 그래디언트를 전치하여 나열한 형태가 됨.

$$
\mathbf{J} = \begin{bmatrix} \frac{\partial f_1}{\partial x} & \frac{\partial f_1}{\partial y} & \frac{\partial f_1}{\partial z} \\ \frac{\partial f_2}{\partial x} & \frac{\partial f_2}{\partial y} & \frac{\partial f_2}{\partial z} \end{bmatrix} = \begin{bmatrix} 2xy & x^2 & 0 \\ 1 & 0 & 3z^2 \end{bmatrix} \in \mathbb{R}^{2 \times 3}
$$
