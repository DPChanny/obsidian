---
tags: []
related:
  - "[[Autograd|Autograd]]"
  - "[[Jacobian Matrix|Jacobian Matrix]]"
---

벡터나 행렬을 넘어선 고차원 데이터(Tensor) 간의 다변수 미분 규칙과 컴퓨팅 연산 처리 방식에 대한 정리임.

## 다차원 텐서의 미분 차원 규칙

스칼라(0차원)와 벡터(1차원) 간의 미분 결과가 다변수 함수의 일반화인 야코비안 행렬(2차원)이 되듯, 그 이상의 고차원 텐서 미분 역시 일관된 차원 결합 원리를 따름.

일반적으로 텐서 간 미분 결과의 텐서 차원(Order)은 다음과 같이 결정됨.

$$
 \text{미분 결과의 차원} = \text{출력 텐서 차원} + \text{입력 텐서 차원} 
$$

### 고차원 텐서 미분 예시

출력이 3차원 텐서 $\mathcal{Y} \in \mathbb{R}^{m \times n \times k}$ 이고, 입력이 2차원 텐서(행렬) $\mathbf{X} \in \mathbb{R}^{p \times q}$ 인 함수 $\mathcal{Y} = f(\mathbf{X})$ 를 가정함.

이때의 미분 결과인 도함수 텐서 $\mathcal{D} = \frac{\partial \mathcal{Y}}{\partial \mathbf{X}}$ 는 차원이 크기 $m \times n \times k \times p \times q$ 에 달하는 **5차원 텐서 (Order-5 Tensor)** 가 됨. ($3 + 2 = 5$)

그 내부의 각 단일 스칼라 성분(Component)은 인덱스 표기법을 통해 다음과 같이 정의됨.

$$
 \mathcal{D}_{ijkpq} = \frac{\partial \mathcal{Y}_{ijk}}{\partial \mathbf{X}_{pq}} 
$$

- $\mathcal{Y}_{ijk}$: 출력 3차원 텐서의 특정 위치에 있는 스칼라 성분.
- $\mathbf{X}_{pq}$: 입력 행렬의 특정 위치에 있는 스칼라 성분.

## 고차원 텐서 미분의 연산 기법

5차원, 6차원과 같은 고차원 도함수는 종이에 행렬 형태로 나열하기 불가능에 가까우며, 프로그래밍 계산 시에도 차원 구조를 유지하며 연산하는 것이 비효율적임. 따라서 실제 수학적 증명이나 내부 컴퓨팅 라이브러리(NumPy, PyTorch 등)에서는 다음과 같은 기법을 활용함.

1. **벡터화 (Vectorization)**
   - 입력된 2차원 행렬과 출력된 3차원 텐서를 모두 1차원 벡터로 길게 펼침(Flatten).
   - 예를 들어, $\text{vec}(\mathcal{Y})$ 와 $\text{vec}(\mathbf{X})$ 간의 미분으로 치환하여, 우리가 잘 알고 있는 거대한 2차원 야코비안 행렬 형태로 만들어 연산함.

2. **아인슈타인 표기법 (Einstein Summation Convention)**
   - $\Sigma$ 기호를 생략하고 반복되는 인덱스에 대해 덧셈을 암묵적으로 수행하는 표기법.
   - 행렬을 1차원으로 펴지 않고도, 각 텐서의 인덱스끼리 바로 곱하고 더하여 다차원 연산을 매우 간결하게 표현하고 효율적으로 계산할 수 있음. (예: `torch.einsum`)

---

※ **참고**: 딥러닝에서 배치나 시계열 차원을 포함한 파라미터 미분을 수행할 때는, 위와 같은 거대 다차원 텐서를 직접 구축하지 않고 '스칼라 환원'과 '벡터-야코비안 곱(VJP)'을 통해 메모리 한계를 우회함. (자세한 내용은 [[Autograd|Autograd]] 문서 참조)
