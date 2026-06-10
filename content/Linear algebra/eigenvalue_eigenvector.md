---
layout: post
title: "1. 고유값, 고유벡터, 그리고 PCA 개요"
date: 2026-06-08 21:00:00 +0900
categories: [Linear algebra]
tags: [Eigenvalue, Eigenvector, PCA, Eigendecomposition, Null Space, 선형대수, 고유값, 고유벡터]
math: true
toc: true
---

## 고유벡터란 무엇인가

행렬 $A$를 곱했을 때 **방향은 유지되고 크기만 변하는 벡터**를 고유벡터(eigenvector)라고 한다.

$$A \mathbf{v} = \lambda \mathbf{v}$$

- $A$: 변환 행렬
- $\mathbf{v}$: 고유벡터 (영벡터가 아닌)
- $\lambda$: 고유값 (scalar)

일반적인 벡터에 행렬을 곱하면 방향과 크기가 모두 바뀐다. 그런데 고유벡터 방향으로 들어오는 벡터는 **방향을 건드리지 않고 크기만 $\lambda$배** 된다.

$\lambda$의 의미:

| 조건 | 의미 |
|------|------|
| $\lambda > 1$ | 그 방향으로 늘어남 |
| $0 < \lambda < 1$ | 그 방향으로 줄어듦 |
| $\lambda < 0$ | 방향 반전 후 스케일 |
| $\lambda = 0$ | 그 방향으로 완전히 찌그러짐 |

---

## 왜 구하는가

고유벡터는 **행렬이 가장 단순하게 작동하는 방향**이다. 이 방향을 찾으면 복잡한 변환이 스칼라 곱 하나로 줄어든다.

### 동기 1: 반복 변환

$A$를 100번 곱하면?

일반 벡터는 매번 방향·크기를 전부 계산해야 한다. 고유벡터 $\mathbf{e}$ 방향에서는:

$$A^{100} \mathbf{e} = \lambda^{100} \mathbf{e}$$

$\lambda$의 거듭제곱만 계산하면 끝이다. 이게 가능한 이유는 **대각화(diagonalization)** 때문이다.

$$A = V \Lambda V^{-1} \implies A^n = V \Lambda^n V^{-1}$$

$V^{-1}V = I$ 가 계속 소거되어 $\Lambda^n$ 만 남는다. 대각행렬의 거듭제곱은 각 원소를 $n$제곱하면 끝.

$$\Lambda^n = \begin{bmatrix} \lambda_1^n & 0 \\ 0 & \lambda_2^n \end{bmatrix}$$

장기 거동은 절댓값이 가장 큰 $\lambda$의 고유벡터 방향이 지배한다 — 이를 지배 고유벡터(dominant eigenvector)라고 한다.

#### Eigendecomposition의 좋은 점 5가지

대각화 $A = V\Lambda V^{-1}$가 성립할 때 얻는 이점들이다.

**① 거듭제곱: $A^k = V\Lambda^k V^{-1}$**

$$A^k = \underbrace{(V\Lambda V^{-1})(V\Lambda V^{-1})\cdots(V\Lambda V^{-1})}_{k\text{번}} = V\Lambda^k V^{-1}$$

중간의 $V^{-1}V = I$가 모두 소거되어 $\Lambda$만 $k$번 거듭제곱하면 된다. $\Lambda^k = \text{diag}(\lambda_1^k, \lambda_2^k, \ldots, \lambda_n^k)$이므로 계산 비용이 극적으로 줄어든다.

$$\begin{bmatrix} \lambda_1 & \\ & \lambda_2 \end{bmatrix}^k = \begin{bmatrix} \lambda_1^k & \\ & \lambda_2^k \end{bmatrix}$$

**② 역행렬: $A^{-1} = V\Lambda^{-1}V^{-1}$**

$A = V\Lambda V^{-1}$의 양변을 역행렬로 뒤집으면:

$$A^{-1} = (V\Lambda V^{-1})^{-1} = V\Lambda^{-1}V^{-1}$$

$\Lambda^{-1} = \text{diag}(1/\lambda_1, 1/\lambda_2, \ldots, 1/\lambda_n)$이므로, $A$의 역행렬을 구하는 문제가 스칼라의 역수 계산으로 줄어든다. 단, 모든 $\lambda_i \neq 0$이어야 역행렬이 존재한다.

**③ 행렬식: $\det(A) = \prod_{i} \lambda_i$**

$$\det(A) = \det(V\Lambda V^{-1}) = \det(V)\det(\Lambda)\det(V^{-1}) = \det(\Lambda) = \prod_{i=1}^{n} \lambda_i$$

$\det(V)\det(V^{-1}) = \det(VV^{-1}) = \det(I) = 1$이므로 소거된다. 대각행렬의 행렬식은 대각 원소의 곱.

**④ 대각합: $\text{tr}(A) = \sum_{i} \lambda_i$**

$$\text{tr}(A) = \text{tr}(V\Lambda V^{-1}) = \text{tr}(\Lambda V^{-1}V) = \text{tr}(\Lambda) = \sum_{i=1}^{n} \lambda_i$$

trace의 순환 성질 $\text{tr}(ABC) = \text{tr}(CAB)$를 이용해 $V^{-1}V = I$로 소거. trace는 닮음 변환에 불변이므로, 어떤 기저로 표현하든 $A$의 trace는 고유값의 합과 같다.

**⑤ rank-deficiency 판별: $\text{rank-deficient} \iff \det(A) = 0 \iff \lambda_i = 0$인 고유값 존재**

③에서 $\det(A) = \prod \lambda_i$이므로:

$$\det(A) = 0 \iff \text{어떤 } \lambda_i = 0$$

$\det(A) = 0$은 $A$가 full rank가 아님과 동치이다. 즉, **$\lambda = 0$인 고유값이 존재한다는 것은 그 방향으로 $A$가 공간을 찌그러뜨린다는 뜻**이며, 해당 고유벡터들이 null space를 구성한다. PCA에서 $\lambda = 0$인 성분을 버리는 것도 같은 맥락 — 그 방향에는 분산이 없다.

![혁펜하임](images/eigendecomposition.png)

---

### 동기 2: 분해와 압축 (PCA)

데이터가 100차원일 때, 어떤 방향이 분산을 가장 많이 담고 있는가? 공분산 행렬 $\Sigma$를 고유분해하면 그 답이 나온다.

$$\Sigma \mathbf{e}_k = \lambda_k \mathbf{e}_k$$

$\lambda_k$는 $\mathbf{e}_k$ 방향의 분산량 그 자체다. 상위 $k$개 $(\lambda, \mathbf{e})$ 쌍만 남기면 정보 손실을 최소화하면서 차원을 줄일 수 있다.

**공분산 행렬은 어떻게 만드나?**

데이터 행렬 $X$ ($n \times p$, 중심화 완료)에서:

$$\Sigma = \frac{1}{n} X^T X$$

$(i,j)$ 원소를 직접 풀면:

$$\Sigma_{ij} = \frac{1}{n}(X^T X)_{ij} = \frac{1}{n} \sum_{k=1}^{n} x_{ki} x_{kj} = \text{Cov}(X_i, X_j)$$

분산 공식 $\text{Var}(X) = \frac{1}{n}\sum x_k^2 = \frac{1}{n}\mathbf{x}^T\mathbf{x}$ 와 구조가 같다. 중심화 후엔 곱의 합이 곧 공분산이므로, $X^TX$ 가 모든 변수 쌍의 공분산을 한 번에 계산해준다.

**$\Sigma$의 두 가지 성질**

$\Sigma_{ij} = \Sigma_{ji}$ 이므로 항상 **symmetric**. Spectral Theorem에 의해 고유벡터들이 서로 직교하고 $\Sigma = P\Lambda P^T$ ($P^{-1} = P^T$)로 대각화된다. PCA 주성분들이 uncorrelated인 이유가 여기서 나온다.

Full rank는 보장되지 않는다. $p > n$ 이면 $X$의 열벡터 $p$개가 $n$차원 공간 안에 있으므로 rank가 최대 $n$까지밖에 안 올라간다. $p \times p$ 행렬이 full rank가 되려면 rank $= p$ 여야 하는데 불가능. 변수 간 완전한 선형관계가 있을 때도 마찬가지다. 이 경우 $\lambda = 0$인 고유값이 생기는데, 그 방향은 분산이 없다는 뜻이므로 PCA에서 자연스럽게 제거된다. 따라서 $\Sigma$는 **positive semi-definite (PSD)**, 즉 $\mathbf{v}^T \Sigma \mathbf{v} \geq 0$.

column space 관점에서 보면 더 명확하다. $\Sigma\mathbf{v}$의 결과는 항상 $\Sigma$의 column space 안에 있고, column space의 차원 = rank $= r$이다. $p > n$이면 $r \leq n < p$ 이므로 $\Sigma$는 $p$차원 공간을 최대 $n$차원 부분공간으로 찌그러뜨린다. 나머지 $p - r$개 방향은 $\Sigma$가 $\mathbf{0}$으로 보내버리는데, 그 방향들이 바로 $\lambda = 0$인 고유벡터들이다 — null space와 column space의 orthogonal complement가 일치하기 때문이다. full rank이면 null space $= \{\mathbf{0}\}$이므로 $\lambda = 0$인 고유값 자체가 존재할 수 없다.

**$\lambda_k$ 가 분산인 이유**

고유벡터를 단위벡터로 정규화($\|\mathbf{e}_k\| = 1$)한 후, 데이터를 $\mathbf{e}_k$ 방향으로 투영하면:

$$z_k = X\mathbf{e}_k \quad (n \times 1)$$

$z_k$의 분산을 계산하려면 평균을 빼야 한다. 그런데 $X$가 중심화되어 있으면 $\bar{z}_k = 0$ 이 자동으로 성립한다.

$$\bar{z}_k = \frac{1}{n}\mathbf{1}^T z_k = \frac{1}{n}\mathbf{1}^T (X\mathbf{e}_k) = \underbrace{\left(\frac{1}{n}\mathbf{1}^T X\right)}_{\bar{\mathbf{x}}^T = \mathbf{0}^T} \mathbf{e}_k = 0$$

$\frac{1}{n}\mathbf{1}^T X$ 는 $X$ 각 열의 평균을 행벡터로 나열한 것 — 중심화되어 있으면 전부 0. 즉 **$X$ 중심화 → 각 변수 평균 0 → 그 선형결합 $z_k$ 평균도 0**.

따라서 분산은:

$$\text{Var}(z_k) = \frac{1}{n}z_k^T z_k = \mathbf{e}_k^T \Sigma \mathbf{e}_k = \mathbf{e}_k^T (\lambda_k \mathbf{e}_k) = \lambda_k \|\mathbf{e}_k\|^2 = \lambda_k$$

**$\lambda_k / \sum \lambda_i$ 가 기여율인 이유**

먼저 "분산이 크다 = 기여율이 높다"가 왜 성립하는지부터. 분산은 데이터가 그 방향으로 얼마나 퍼져 있는가다. 키·몸무게 데이터를 예로 들면, 키 방향으로 데이터가 넓게 퍼져 있으면 분산이 크고 사람마다 차이가 많이 난다 — 이 방향이 개인을 구분하는 데 유용하다. 반대로 체온 방향은 거의 안 퍼져 있으면 분산이 작고 사람마다 차이가 없다 — 이 방향은 데이터 구조를 설명하는 데 쓸모없다. **분산이 크다 = 데이터의 변동이 그 방향에 집중 = 그 방향이 데이터 구조를 설명한다.** $\lambda = 0$인 방향은 모든 데이터가 같은 값이므로 버려도 정보 손실이 없다.

고유벡터들이 직교하고 전체 공간을 span하므로 전체 분산은 각 방향 분산의 합으로 완전히 분해된다:

$$\text{tr}(\Sigma) = \text{tr}(P\Lambda P^T) = \text{tr}(\Lambda) = \sum_{i=1}^{p} \lambda_i$$

직교 분해라 성분 간 겹침이 없으므로:

$$\frac{\lambda_k}{\sum_i \lambda_i} = \frac{k\text{번째 방향의 분산}}{\text{전체 분산}}$$

직교성이 없으면 분산이 겹쳐서 이런 분해가 성립하지 않는다.

### 동기 3: 안정 상태 탐색

네트워크(마르코프 체인, PageRank)가 수렴하면 어떤 상태에 머무는가?

$$A \cdot \pi = 1 \cdot \pi$$

$\lambda = 1$ 인 고유벡터 $\pi$ 가 시스템의 고정점(fixed point)이다. 어떤 초기 분포에서 출발해도 반복 적용 시 이 $\pi$로 수렴한다. Google PageRank의 랭킹 점수가 바로 이 고유벡터의 각 원소값이다.

확률 전이 행렬은 각 열의 합이 1(확률 보존)이므로 구조적으로 $\lambda = 1$인 고유값이 반드시 존재한다 — Perron-Frobenius 정리.

---

## 고유값을 어떻게 구하는가

### 핵심 조건

$A\mathbf{v} = \lambda\mathbf{v}$ 에서:

$$A\mathbf{v} - \lambda\mathbf{v} = 0 \implies (A - \lambda I)\mathbf{v} = 0$$

$\mathbf{v} \neq 0$ 인 해가 존재하려면 $(A - \lambda I)$ 가 full rank가 아니어야 한다. 즉:

$$\det(A - \lambda I) = 0$$

이게 **특성방정식(characteristic equation)** 이다.

### 계산 순서

**① $\det(A - \lambda I) = 0$ 으로 $\lambda$ 를 먼저 찾는다**

$\lambda$ 를 모르는 상태에선 $(A - \lambda I)$ 가 미정이라 null space를 구할 수 없다. 먼저 "어떤 $\lambda$ 일 때 이 행렬이 full rank를 잃는가"를 특정해야 한다.

**② 찾은 $\lambda$ 를 대입해 $(A - \lambda I)\mathbf{x} = 0$ 을 푼다**

이 null space가 곧 **eigenspace** — 고유벡터들이 사는 공간이다.

### 예시

$$A = \begin{bmatrix} 3 & 1 \\ 0 & 2 \end{bmatrix}$$

**① 특성방정식:**

$$\det(A - \lambda I) = (3 - \lambda)(2 - \lambda) = 0 \implies \lambda = 3, \quad \lambda = 2$$

**② $\lambda = 3$ 대입:**

$$(A - 3I)\mathbf{x} = \begin{bmatrix} 0 & 1 \\ 0 & -1 \end{bmatrix} \mathbf{x} = 0 \implies \mathbf{v}_1 = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$$

**② $\lambda = 2$ 대입:**

$$(A - 2I)\mathbf{x} = \begin{bmatrix} 1 & 1 \\ 0 & 0 \end{bmatrix} \mathbf{x} = 0 \implies \mathbf{v}_2 = \begin{bmatrix} 1 \\ -1 \end{bmatrix}$$

---

## 연결되는 개념들

**Full rank와 null space**

- Full rank 행렬의 null space = $\{\mathbf{0}\}$ (영벡터뿐)
- $A - \lambda I$ 는 고유값 $\lambda$ 에서 반드시 full rank가 아님
- $\det(A - \lambda I) = 0$ ↔ full rank 아님 ↔ $A^{-1}$ 존재하지 않음 — 전부 동치

**대각화와 $A^n$**

$$A^n = V \Lambda^n V^{-1}, \quad \Lambda^n = \begin{bmatrix} \lambda_1^n & 0 \\ 0 & \lambda_2^n \end{bmatrix}$$

**Eigenspace**

$(A - \lambda I)\mathbf{x} = 0$ 의 null space. 같은 $\lambda$ 에 대응하는 모든 고유벡터 + 영벡터로 구성된 부분공간.