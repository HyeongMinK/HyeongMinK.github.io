---
layout: post
title: "3. 주성분 분석 (PCA: Principal Component Analysis)"
date: 2026-06-10 21:00:00 +0900
categories: [Linear algebra]
tags: [PCA, eigenvalue, eigenvector, covariance, projection, 선형대수, 주성분분석]
math: true
toc: true
---

## 문제 정의

데이터가 2D로 퍼져 있을 때, 이걸 1D로 줄이려면 어떤 방향으로 내려야 할까?

$$① \text{ 왜 분산이 가장 큰 방향인가?} \qquad ② \text{ 왜 그 다음은 수직 방향인가?}$$

---

## 왜 분산이 가장 큰 방향인가

### 정보 손실의 의미

데이터 $d_i \in \mathbb{R}^p$를 단위벡터 $u$ 방향으로 projection하면 2D 점이 1D 숫자 하나로 줄어든다:

$$d_i \xrightarrow{\text{projection}} (d_i^\top u) \cdot u$$

어떤 방향 $u$를 고르느냐에 따라 결과가 달라진다. 예를 들어 데이터가 대각선으로 퍼져 있는데 수평선으로 내리면 점들이 한 곳에 몰려버린다 — 원래는 다 달랐던 점들이 같은 값으로 뭉개지는 것, 이게 정보 손실이다.

분산이 클수록 projection 후에도 점들이 넓게 퍼져 있다 = 개별 데이터가 잘 구분된다 = 정보가 보존된다.

### 오차 벡터는 왜 수직인가

$d_i$를 $u$ 방향으로 projection한 점을 $P$라 하면:

$$P = (d_i^\top u)\, u$$

오차 벡터는 원래 점에서 $P$를 뺀 것이다:

$$\text{오차} = d_i - (d_i^\top u)\,u$$

이 오차가 $u$에 수직임을 내적으로 확인하면:

$$\bigl(d_i - (d_i^\top u)\,u\bigr)^\top u = d_i^\top u - d_i^\top u \cdot \underbrace{u^\top u}_{=1} = 0$$

내적이 0 = 직교. **$P$는 직선 위에서 $d_i$와 가장 가까운 점(수선의 발)이기 때문에 오차는 항상 $u$에 수직**이다.

### 재구성 오차 최소화

방향 $u$로 projection했다가 다시 복원할 때의 오차를 최소화하는 문제로 쓸 수 있다.

데이터 $d_i$, 단위벡터 $u$ ($\|u\|_2 = 1$)에 대해:

$$\min_u \frac{1}{N} \sum_i \|d_i - (d_i^\top u)\,u\|^2 \quad \text{s.t.} \quad u^\top u = 1$$

**전개 과정.** 오차의 제곱을 풀면:

$$\|d_i - (d_i^\top u)\,u\|^2 = d_i^\top d_i - 2(d_i^\top u)^2 + (d_i^\top u)^2\underbrace{u^\top u}_{=1}$$

$-2$와 $+1$이 합쳐져서:

$$= d_i^\top d_i - (d_i^\top u)^2$$

$\frac{1}{N}\sum_i$로 묶으면:

$$\frac{1}{N}\sum_i d_i^\top d_i - \frac{1}{N}\sum_i (d_i^\top u)^2$$

첫 항은 $u$와 무관한 상수이므로 최소화 대상에서 빠진다. $(d_i^\top u)^2 = u^\top d_i d_i^\top u$로 쓰면:

$$\min_u \left(-\frac{1}{N}\sum_i u^\top d_i d_i^\top u\right)$$

중심화($d_i - \bar{d}$)까지 적용하면:

$$= -u^\top \underbrace{\left[\frac{1}{N}\sum_i (d_i - \bar{d})(d_i - \bar{d})^\top\right]}_{R_d \;=\; \text{Sample Covariance Matrix}} u = -u^\top R_d\, u$$

앞에 $-$가 붙어 있으니:

$$\min_u (-u^\top R_d u) \iff \max_u (u^\top R_d u)$$

**재구성 오차 최소화 = 분산 최대화**가 동치다.

![혁펜하임](images/PCA.png)

---

## 라그랑주 승수법으로 풀기

제약 $u^\top u = 1$이 있는 최대화 문제다:

$$\max_u \; u^\top R_d u \quad \text{s.t.} \quad u^\top u = 1$$

라그랑지안을 세우면:

$$\mathcal{L}(u, \lambda) = u^\top R_d u - \lambda(u^\top u - 1)$$

$u$에 대해 미분해서 0으로 놓으면:

$$\frac{\partial \mathcal{L}}{\partial u} = 2R_d u - 2\lambda u = 0 \implies R_d u = \lambda u$$

이게 바로 **고유값 방정식**이다. $u$가 $R_d$의 고유벡터, $\lambda$가 고유값.

목적함수에 대입하면:

$$u^\top R_d u = u^\top(\lambda u) = \lambda \underbrace{u^\top u}_{=1} = \lambda$$

최대화하려면 $\lambda$가 최대여야 하므로 — **가장 큰 고유값 $\lambda_1$에 대응하는 고유벡터 $u_1$이 첫 번째 주성분**이다.

---

## 왜 두 번째는 수직 방향인가

두 번째 주성분은 첫 번째로 설명한 분산을 제외하고, 나머지 분산을 가장 많이 설명하는 방향이다.

수학적으로는 $u_1$에 직교한다는 제약을 추가해서 같은 문제를 풀면:

$$\max_u \; u^\top R_d u \quad \text{s.t.} \quad u^\top u = 1, \; u^\top u_1 = 0$$

라그랑주를 다시 쓰면 결국 $R_d u = \lambda u$로 귀결되고, 이번엔 두 번째로 큰 고유값 $\lambda_2$에 대응하는 고유벡터 $u_2$가 답이 된다.

스펙트럴 정리에 의해 $R_d$는 실수 대칭행렬이므로 고유벡터들이 자동으로 직교한다. 즉 **두 번째 주성분이 수직인 것은 공분산 행렬의 성질에서 자연스럽게 나오는 결과**다.

---

## 전체 흐름 요약

$$\Sigma \text{(공분산 행렬)} \xrightarrow{\text{eigendecomposition}} \lambda_1 \geq \lambda_2 \geq \cdots \geq \lambda_p$$

| 주성분 | 방향 | 설명하는 분산 | 기여율 |
|:---:|---|---|---|
| 1st PC | $u_1$ (최대 $\lambda_1$의 고유벡터) | $\lambda_1$ | $\lambda_1 / \sum \lambda_i$ |
| 2nd PC | $u_2$ (두 번째 $\lambda_2$의 고유벡터) | $\lambda_2$ | $\lambda_2 / \sum \lambda_i$ |
| $k$th PC | $u_k$ | $\lambda_k$ | $\lambda_k / \sum \lambda_i$ |

상위 $k$개만 남기면 $p$차원 → $k$차원으로 축소된다:

$$X_{\text{reduced}} = X U_k \qquad (U_k = [u_1 \mid u_2 \mid \cdots \mid u_k])$$

정보 손실을 최소화하면서 차원을 줄이는 것 — 그게 PCA다.
