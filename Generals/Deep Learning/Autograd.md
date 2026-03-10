---
related:
  - "[[Mathematics/Calculus/Tensor Caclulus|Tensor Caclulus]]"
  - "[[Mathematics/Linear Algebra/Layout|Layout]]"
---

PyTorch, TensorFlow 등 자동 미분(Automatic Differentiation) 프레임워크가 고차원 텐서와 대규모 배치(Batch), 시계열(Time) 데이터를 처리할 때 메모리 한계를 우회하고 효율적으로 그래디언트를 구하는 연산 기법에 대한 정리임.

## 고차원 텐서 미분의 근본적 문제

다차원 텐서 미분 규칙에 따르면 2차원 파라미터 행렬(예: 가중치)로 3차원 출력(예: 배치 포함 데이터)을 미분하면 5차원 텐서가 생성됨.

만약 수만 개의 배치 사이즈나 긴 시계열 데이터를 가진 딥러닝 모델에서 이런 고차원 야코비안 행렬을 메모리에 직접 생성하려 한다면, 물리적인 메모리 한계(OOM, Out Of Memory)에 직면하게 됨.

Autograd 메커니즘은 이런 거대한 다차원 도함수를 명시적으로 구축하지 않고 다음 세 가지 핵심 기법을 통해 회피함.

## 1. 최종 손실의 스칼라 환원 (Reduction to Scalar)

딥러닝의 역전파(Backpropagation)는 네트워크의 최상단에 있는 최종 손실 함수(Loss Function)에서부터 출발함.

이때 손실 함수는 반드시 `.mean()` 이나 `.sum()` 과 같은 집계 연산을 거쳐 단일 **스칼라($0$차원 텐서)** 로 환원됨.

기계학습 분야에서 주로 사용하는 '분모 중심 표기법(Denominator-layout)'에 따르면, 스칼라($L$)를 특정 형태의 텐서($\mathbf{W}$)로 미분한 결과 $\nabla_{\mathbf{W}} L$ 은 항상 미분하는 **분모($\mathbf{W}$)와 완벽하게 동일한 차원 및 형태(`.grad`)** 를 가짐.

즉, 역전파 과정의 최종 도달점인 파라미터 미분값은, 연산 과정 중간에 데이터가 아무리 복잡한 3차원(이미지), 4차원(배치+이미지) 이었더라도 최종적으로는 자기 자신($\mathbf{W}$)의 크기와 동일하게 수렴하므로 고차원 텐서를 유지할 필요가 없음.

## 2. 벡터-야코비안 곱 (VJP, Vector-Jacobian Product)의 후진 탐색

자동 미분(특히 후진 모드, Reverse-mode Autodiff)은 각 계층(Layer)의 전체 야코비안 $\mathbf{J}$ 를 별도로 생성하고 보관하는 방식을 쓰지 않음.

대신, 연쇄 법칙(Chain Rule)에 따라 상위 계층에서 흘러 내려온 손실의 기울기 벡터 $\mathbf{v}$ 와 현재 계층의 야코비안 $\mathbf{J}$ 를 곱하는 **$\mathbf{v}^T \mathbf{J}$ 형태의 내적 연산(VJP)** 만을 수행함. 이 벡터-행렬 곱을 통해 거대한 야코비안 행렬 전체를 인스턴스화하지 않고도, 최종 스칼라 손실에 대한 기울기를 하위 계층으로 효율적으로 전파할 수 있음.

## 3. 독립 차원에 대한 암묵적 누적 (Implicit Summation)

배치(Batch), 시계열(Time), 공간 차원(Spatial)과 같이 데이터 포인트가 독립적으로 존재하는 차원을 처리할 때, 데이터를 반복문(for-loop)으로 잘라서 그래디언트를 구한 뒤 더하는 방식은 매우 비효율적임.

미분의 선형성(Linearity of Differentiation)에 의해 $\frac{\partial}{\partial \mathbf{W}} \sum L_i = \sum \frac{\partial L_i}{\partial \mathbf{W}}$ 이 성립함. 프레임워크는 이를 활용해 거대한 행렬/텐서 곱셈 연산 내부에 배치나 시계열 차원을 병합하여 한 번에 처리함.

1. **배치 차원 (Batch)**: 선형 계층의 역전파 시 발생하는 행렬 곱셈($\mathbf{X}^T \mathbf{dY}$) 연산 그 자체에 배치 사이즈 $N$ 에 대한 미분값들의 **합산(Sum)** 과정이 수학적으로 내포되어 있음. 단 한 번의 행렬 내적으로 전체 배치의 그래디언트가 올바르게 누적됨.
2. **시계열 차원 (Time)**: RNN이나 LSTM의 BPTT(Backpropagation Through Time) 연산에서도, 언롤링된 시간축을 따라 계산된 VJP 결과들이 해당 파라미터의 `.grad` 버퍼에 계속해서 누적(Accumulate)되는 방식으로 합산됨.
3. **공간 차원 (Spatial/3D)**: CNN의 역전파 과정인 교차 상관(Cross-correlation) 연산 내부에서 필터가 이동하는 모든 공간적 위치(가로, 세로, 깊이)에 대한 그래디언트가 자동으로 합산되어 필터와 동일한 크기의 기울기 텐서만을 반환함.
