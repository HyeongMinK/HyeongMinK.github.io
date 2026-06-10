---
layout: post
title: "행렬 대각화: 조건, 충분조건, 직교성"
date: 2025-01-01
categories: [linear-algebra]
tags: [eigenvalue, eigenvector, diagonalization, symmetric-matrix]
---

## 전제 조건

대각화는 **정방행렬(n×n)에서만 정의**된다. A = PDP⁻¹에서 P⁻¹가 존재해야 하기 때문이다. 직사각형 행렬에는 대각화 개념 자체가 정의되지 않는다(SVD는 별개).

**Full rank는 불필요하다.** 대각화 가능성은 A의 역행렬 존재 여부와 무관하다. 영행렬(rank 0)은 D = 0 자체가 대각행렬이므로 trivially 대각화 가능하고, diag(0, 0, 1)처럼 rank가 부족해도 대각화에 문제가 없다. Full rank 조건은 A가 아닌 고유벡터 행렬 P에 붙는 조건이다.

---

## 필요충분조건 (iff)

$$A \text{가 대각화 가능} \iff \text{고유벡터 } n \text{개가 선형독립}$$

즉, $A = PDP^{-1}$ 분해가 존재하는 것과 동치이다. 여기서

$$P = [v_1 \mid v_2 \mid \cdots \mid v_n], \quad D = \text{diag}(\lambda_1, \lambda_2, \ldots, \lambda_n)$$

이 조건의 동치 표현들:

- **대수적 중복도 = 기하적 중복도**: 각 고유값 $\lambda_i$에 대해 $\dim E_{\lambda_i} = m(\lambda_i)$
- **고유공간 직합**: $E_{\lambda_1} \oplus E_{\lambda_2} \oplus \cdots \oplus E_{\lambda_k} = \mathbb{R}^n$
- **최소다항식**: 서로 다른 1차 인수들의 곱으로 인수분해됨

---

## 충분조건

이것만 성립하면 대각화 가능이 **자동 보장**된다.

### 서로 다른 n개의 고유값

$$\lambda_1 \neq \lambda_2 \neq \cdots \neq \lambda_n \implies \text{고유벡터 자동으로 선형독립}$$

역은 성립하지 않는다. 반복 고유값이 있어도 대각화 가능한 경우가 존재한다(예: 단위행렬).

### 실수 대칭행렬

$$\text{Symmetric matrix} \implies \text{diagonalizable}$$

실수 대칭행렬 $A = A^\top$은 **항상 대각화 가능**하다. 그런데 일반 대각화보다 더 강한 결과가 성립한다 — 고유벡터 행렬 $Q$가 **직교행렬**이 된다.

$$A = Q\Lambda Q^\top \qquad (Q^{-1} = Q^\top)$$

일반 eigendecomposition $A = V\Lambda V^{-1}$과 비교하면, symmetric인 경우 $V^{-1} = V^\top$이 자동으로 성립한다. $V^{-1}$를 직접 계산할 필요 없이 전치만 하면 끝이다.

#### 스펙트럴 정리 (Spectral Theorem)

이것이 가능한 근거가 스펙트럴 정리다. 실수 대칭행렬 $A = A^\top \in \mathbb{R}^{n \times n}$에 대해:

1. **고유값이 모두 실수**다.
2. **서로 다른 고유값에 대응하는 고유벡터는 직교**한다.
3. **직교 대각화 가능** — 직교행렬 $Q$ ($Q^{-1} = Q^\top$)가 존재해서 $A = Q\Lambda Q^\top$.

**왜 고유값이 실수인가?**

먼저 용어 정리. 복소수 $z = a + bi$에서 **켤레** $\bar{z} = a - bi$ — 허수부 부호만 뒤집은 것이다. 실수는 허수부가 0이므로 켤레가 자기 자신이다: $z \in \mathbb{R} \iff \bar{z} = z$.

벡터의 **켤레전치** $\bar{v}^\top$은 각 원소에 켤레를 취한 뒤 전치한 것이다. 예를 들어:

$$v = \begin{pmatrix} 1+i \\ 2 \end{pmatrix} \implies \bar{v}^\top = \begin{pmatrix} 1-i & 2 \end{pmatrix}$$

이를 $v$에 곱하면:

$$\bar{v}^\top v = (1-i)(1+i) + 2 \cdot 2 = (1+1) + 4 = 6 = \|v\|^2$$

허수부가 완전히 소거되어 $\bar{v}^\top v = \|v\|^2 \geq 0$이 항상 성립한다.

---

이제 본론. $Av = \lambda v$의 양변 왼쪽에 $\bar{v}^\top$을 곱하면:

$$\bar{v}^\top A v = \lambda \underbrace{\bar{v}^\top v}_{= \|v\|^2} = \lambda \|v\|^2$$

$\|v\|^2 > 0$이므로 $\lambda = \dfrac{\bar{v}^\top A v}{\|v\|^2}$. 따라서 **$\bar{v}^\top A v$가 실수임을 보이면 $\lambda \in \mathbb{R}$이 따라온다.**

$s = \bar{v}^\top A v$로 놓자. $s$는 스칼라(1×1)이므로 전치해도 값이 안 변한다:

$$s = s^\top = (\bar{v}^\top A v)^\top = v^\top A^\top \bar{v}$$

$A$가 실수 대칭이므로 $A^\top = A$:

$$s^\top = v^\top A \bar{v}$$

이제 $\bar{s}$를 구한다. $A$가 실수 행렬이므로 $\bar{A} = A$:

$$\bar{s} = \overline{v^\top A \bar{v}} = \bar{v}^\top \bar{A} v = \bar{v}^\top A v = s$$

$\bar{s} = s$이므로 $s \in \mathbb{R}$. 따라서 $\lambda \in \mathbb{R}$. $\square$

핵심은 대칭($A^\top = A$)과 실수($\bar{A} = A$) 두 조건이 동시에 작동해서 허수부가 소거된다는 것이다.

**왜 서로 다른 고유값의 고유벡터는 직교인가?**

$Av_1 = \lambda_1 v_1$, $Av_2 = \lambda_2 v_2$, $\lambda_1 \neq \lambda_2$일 때:

$$\lambda_1 v_1^\top v_2 = (Av_1)^\top v_2 = v_1^\top A^\top v_2 = v_1^\top A v_2 = \lambda_2 v_1^\top v_2$$

$$(\lambda_1 - \lambda_2) v_1^\top v_2 = 0 \implies v_1^\top v_2 = 0$$

반복 고유값이 있는 경우에도 Gram-Schmidt로 해당 고유공간 내에서 직교 기저를 항상 구성할 수 있다.

#### Spectral decomposition: rank-1 분해

$A = Q\Lambda Q^\top$를 행렬곱으로 직접 전개하면 흥미로운 구조가 드러난다.

$$A = [q_1\ q_2\ q_3] \begin{bmatrix} \lambda_1 & & \\ & \lambda_2 & \\ & & \lambda_3 \end{bmatrix} \begin{bmatrix} q_1^\top \\ q_2^\top \\ q_3^\top \end{bmatrix} = [\lambda_1 q_1\ \lambda_2 q_2\ \lambda_3 q_3] \begin{bmatrix} q_1^\top \\ q_2^\top \\ q_3^\top \end{bmatrix}$$

이를 전개하면:

$$\boxed{A = \lambda_1 q_1 q_1^\top + \lambda_2 q_2 q_2^\top + \lambda_3 q_3 q_3^\top = \sum_{i=1}^{n} \lambda_i q_i q_i^\top}$$

각 항 $q_i q_i^\top$은 열벡터 × 행벡터 = **rank-1 행렬**이다. 즉 $A$는 rank-1 행렬들의 가중합으로 완전히 분해된다 — 가중치가 바로 고유값 $\lambda_i$.

![혁펜하임](images/spectral.png)

**PCA에서의 의미**: 상위 $k$개 항만 남기면:

$$A \approx \sum_{i=1}^{k} \lambda_i q_i q_i^\top$$

$\lambda_i$가 클수록 해당 rank-1 성분이 $A$를 많이 설명한다. 이것이 **저차원 근사(low-rank approximation)** 의 원리이고, PCA에서 주성분 $k$개만 남기는 것과 정확히 같은 논리다.

#### $A$를 통과한다는 것의 의미
 
rank-1 분해 $A = \sum_i \lambda_i q_i q_i^\top$에 벡터 $x$를 넣으면:
 
$$Ax = \lambda_1 q_1 \underbrace{(q_1^\top x)}_{\text{내적}} + \lambda_2 q_2 (q_2^\top x) + \lambda_3 q_3 (q_3^\top x)$$
 
괄호 안 $q_i^\top x$부터 보자. 내적의 기하학적 의미는 $q_i^\top x = \|q_i\|\|x\|\cos\theta$인데, $q_i$는 직교행렬 $Q$의 열벡터라서 크기가 1로 보장된다. 그러면:
 
$$q_i^\top x = \|x\|\cos\theta$$
 
$x$를 $q_i$ 방향으로 내렸을 때의 길이, 즉 **정사영된 성분의 크기**다. $q_i$가 단위벡터가 아니면 $\|q_i\|$가 남아서 순수한 성분이 나오지 않는다.
 
그러면 $q_i(q_i^\top x)$는 그 길이만큼 $q_i$ 방향으로 뻗은 벡터 — $x$를 $q_i$ 방향으로 정사영한 벡터 그 자체다.

```
        x
       /|
      / |
     /  |
    /θ  |
   /    |
  q_i──────
  
  |x|cosθ = q_i 방향 성분
```
 
**표준기저로 먼저 익숙한 예시를 보자.** $x = (1, 2, 3)^\top$은 사실 이렇게 쓸 수 있다:
 
$$x = 1 \cdot \begin{bmatrix}1\\0\\0\end{bmatrix} + 2 \cdot \begin{bmatrix}0\\1\\0\end{bmatrix} + 3 \cdot \begin{bmatrix}0\\0\\1\end{bmatrix}$$
 
각 성분(1, 2, 3)이 표준기저 방향으로의 내적값이고, 거기에 기저 벡터를 곱해서 더한 것이다. $q_1, q_2, q_3$도 서로 직교하는 단위벡터들이므로 구조가 완전히 같다:
 
$$x = (q_1^\top x)\, q_1 + (q_2^\top x)\, q_2 + (q_3^\top x)\, q_3$$
 
$x$를 고유벡터 기저로 분해한 것이다.
 
**$A$를 통과시키면 이 분해에 $\lambda_i$가 끼어든다:**
 
$$x \;\xrightarrow{\;A\;}\; Ax = \lambda_1(q_1^\top x)\,q_1 + \lambda_2(q_2^\top x)\,q_2 + \lambda_3(q_3^\top x)\,q_3$$
 
- **분해**: $x$를 고유벡터 방향으로 내린다 → $q_i^\top x$
- **스케일**: 각 성분을 $\lambda_i$만큼 늘이거나 줄인다 (음수면 방향 반전)
- **재조합**: 스케일된 성분들을 다시 더한다
$A$라는 복잡한 변환이 사실은 **고유벡터 방향별로 쪼개서 → 각각 $\lambda$배 하고 → 다시 합치는** 작업에 불과하다.
 
**특수 케이스: $\lambda_1 = \lambda_2 = \lambda_3 = 1$이면?**
 
$$Ax = 1 \cdot (q_1^\top x)\,q_1 + 1 \cdot (q_2^\top x)\,q_2 + 1 \cdot (q_3^\top x)\,q_3 = x$$
 
분해했다가 스케일 없이 그대로 재조합하니 도로 $x$가 나온다. 수식으로 보면:
 
$$A = Q \cdot I \cdot Q^\top = QQ^\top = I$$
 
$\Lambda$의 대각 원소가 전부 1이면 $A$는 단위행렬이 된다. $x$를 항등행렬에 통과시키면 그대로 $x$가 나오는 것과 일치한다.

![혁펜하임](images/matrix.png)
 
결국 symmetric matrix $A$를 통과시키는 행위는 이렇게 해석할 수 있다: **$A$가 가진 고유벡터들로 $x$를 분해한 다음, 각 방향을 $\lambda$만큼 조절해서 재조합한다.** 행렬이 공간을 어떻게 변형하는지가 고유값과 고유벡터에 완전히 담겨 있다.
 
---


## 충분조건 vs. 필요충분조건

충분조건은 참이면 결론을 보장하지만, 역은 보장하지 않는다.  
필요충분조건은 양방향이 모두 성립한다.

$$\text{(충분)} \quad P \Rightarrow Q \qquad \text{(iff)} \quad P \iff Q$$

대각화 맥락에서:
- "서로 다른 n개의 고유값" → 충분조건 (역 성립 안 함)
- "고유벡터 n개 선형독립" → 필요충분조건

---

## 직교성: 일반 vs. 실수 대칭

일반 대각화 가능 행렬의 고유벡터들은 **선형독립이지만 직교를 보장하지 않는다.**

직교성은 실수 대칭행렬이 주는 추가 성질이다.

**예시** (비대칭, 대각화 가능):

$$A = \begin{pmatrix} 2 & 1 \\ 0 & 3 \end{pmatrix}, \quad v_1 = \begin{pmatrix} 1 \\ 0 \end{pmatrix}, \quad v_2 = \begin{pmatrix} 1 \\ 1 \end{pmatrix}$$

$$v_1 \cdot v_2 = 1 \neq 0 \quad \rightarrow \text{ 선형독립이지만 직교하지 않음}$$

| 조건 | 선형독립 | 직교 | $P^{-1} = P^\top$ |
|---|:---:|:---:|:---:|
| 일반 대각화 가능 | ✓ | — | — |
| 실수 대칭 ($A = A^\top$) | ✓ | ✓ | ✓ |

실수 대칭의 직교 대각화가 강력한 이유는 $P^{-1}$를 직접 계산하지 않아도 되기 때문이다. PCA에서 공분산 행렬을 쓰는 것도 이 맥락이다 — 공분산 행렬이 대칭이므로 고유벡터들이 자동으로 직교 기저를 형성한다.

---

## 대각화 불가 케이스

**결함 행렬(defective matrix)**: 어떤 고유값에 대해 기하적 중복도 < 대수적 중복도인 경우. 이때는 Jordan 표준형으로 가야 한다.

$$\text{예: } \begin{pmatrix} 1 & 1 \\ 0 & 1 \end{pmatrix} \quad \lambda = 1 \text{ (중복도 2)}, \quad \dim E_1 = 1 \quad \rightarrow \text{ 대각화 불가}$$

---

## Eigendecomposition과 대각화의 동치 관계

### 유도 흐름

2×2 행렬 A에 고유값 $\lambda_1, \lambda_2$, 고유벡터 $v_1, v_2$가 있다고 하면:

$$Av_1 = \lambda_1 v_1, \quad Av_2 = \lambda_2 v_2$$

이를 열 단위로 묶으면:

$$A[v_1 \mid v_2] = [\lambda_1 v_1 \mid \lambda_2 v_2]$$

오른쪽을 인수분해하면:

$$A \underbrace{[v_1 \mid v_2]}_{V} = \underbrace{[v_1 \mid v_2]}_{V} \underbrace{\begin{bmatrix} \lambda_1 & 0 \\ 0 & \lambda_2 \end{bmatrix}}_{\Lambda}$$

$$AV = V\Lambda$$

$V$가 invertible하면 (← 고유벡터들이 선형독립인 경우) 양변에 $V^{-1}$을 곱하면:

$$\boxed{A = V \Lambda V^{-1}}$$

이것이 **eigendecomposition**이다.

### 핵심 동치

$$A \text{가 eigendecomposition 가능}$$
$$\iff A = V\Lambda V^{-1} \text{ 분해 존재}$$
$$\iff V \text{가 invertible} \iff \text{independent eigenvector가 } n\text{개}$$
$$\iff A \text{가 diagonalizable}$$

즉, eigendecomposition과 대각화는 **같은 말**이다. $V$가 앞서의 $P$, $\Lambda$가 $D$에 대응된다.

### 표기 정리

| 표기 | 의미 |
|---|---|
| $V$ | 고유벡터를 열로 쌓은 행렬 $[v_1 \mid v_2 \mid \cdots \mid v_n]$ |
| $\Lambda$ | 고유값을 대각에 나열한 행렬 $\text{diag}(\lambda_1, \ldots, \lambda_n)$ |
| $V^{-1}AV = \Lambda$ | $V$가 $A$를 대각화함 |