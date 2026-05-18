---
title: 패널 데이터의 오차 구조와 추정 전략
date: 2026-05-18
tags:
  - OLS
  - GLS
  - FGLS
---

## 1. BLUE 4가지 가정

$$Y_{it} = \alpha + \beta X_{it} + \epsilon_{it}$$

| 가정 | 수식 | 보장하는 것 | 깨지면 |
|---|---|---|---|
| 1 | $E(\epsilon_{it}) = 0$ | Unbiased | 추정량 편향 |
| 2 | $Var(\epsilon_{it}) = \sigma^2$ | Best (등분산) | 효율성 손상 |
| 3 | $Cov(\epsilon_{it}, \epsilon_{js}) = 0$ | Best (무상관) | 표준오차 틀림 |
| 4 | $Cov(X_{it}, \epsilon_{it}) = 0$ | Unbiased | 추정량 편향 |

> **BLUE** = Best Linear Unbiased Estimator
> 가정 1·4 → 불편성 보장 / 가정 2·3 → 효율성(최소분산) 보장

---

## 2. $Var(\epsilon) = \sigma^2 I$ 유도

T개 시점 오차항 벡터:

$$\epsilon = \begin{pmatrix} \epsilon_1 \\ \epsilon_2 \\ \vdots \\ \epsilon_T \end{pmatrix}$$

분산-공분산 행렬:

$$Var(\epsilon) = E(\epsilon\epsilon^T) = \begin{pmatrix} Var(\epsilon_1) & Cov(\epsilon_1,\epsilon_2) & \cdots \\ Cov(\epsilon_2,\epsilon_1) & Var(\epsilon_2) & \cdots \\ \vdots & \vdots & \ddots \end{pmatrix}$$

**가정 2 (등분산)** 적용: 대각 원소 전부 $\sigma^2$

**가정 3 (무상관)** 적용: 비대각 원소 전부 0

$$\therefore Var(\epsilon) = \sigma^2 I$$

> 자기상관 존재 시 비대각 원소가 채워져 $Var(\epsilon) = \sigma^2\Omega$, $\Omega \neq I$

---

## 3. 자기상관 → 분산 추정 오류 → t검정 신뢰 불가

$\hat{\beta}$는 가정 4만 성립하면 **불편** — 자기상관과 무관

$$E(\hat{\beta}) = \beta \quad \checkmark$$

그러나 OLS 분산 추정 공식은 $\Omega = I$를 전제:

$$\widehat{Var(\hat{\beta})}_{OLS} = \hat{\sigma}^2(X^TX)^{-1}$$

실제 분산:

$$Var(\hat{\beta}) = (X^TX)^{-1}X^T\Omega X(X^TX)^{-1}$$

$\Omega \neq I$이면 OLS SE **과소추정** → t통계량 과대 → 1종 오류 과다

$$t = \frac{\hat{\beta}}{SE(\hat{\beta})} \quad \leftarrow SE \text{ 가 틀림}$$

---

## 4. GLS — $\Omega^{-1/2}$ 변환으로 OLS 가정 복원

$Var(\epsilon) = \sigma^2\Omega$일 때, 양변에 $\Omega^{-1/2}$ 곱하기:

$$\Omega^{-1/2}Y = \Omega^{-1/2}X\beta + \Omega^{-1/2}\epsilon$$

변환된 오차항:

$$Var(\Omega^{-1/2}\epsilon) = \Omega^{-1/2} \cdot \sigma^2\Omega \cdot \Omega^{-1/2} = \sigma^2 I \quad \checkmark$$

→ 변환된 데이터로 OLS 적용 → **BLUE**

### AR(1) 오차일 때

$$\epsilon_t = \rho\epsilon_{t-1} + u_t$$

$\Omega^{-1/2}$ 변환 결과:

$$Y_t - \rho Y_{t-1} = \beta(X_t - \rho X_{t-1}) + u_t$$

$\rho$ **하나만 추정**하면 $\Omega$ 전체가 결정:

$$\Omega_{ts} = Cov(\epsilon_t, \epsilon_s) = \rho^{|t-s|}\sigma^2$$

### 패널에서 자기상관만 추정할 때

$\Omega$는 블록 대각 구조:

$$\Omega = \begin{pmatrix} \Omega_i & 0 & \cdots \\ 0 & \Omega_i & \cdots \\ \vdots & \vdots & \ddots \end{pmatrix}, \quad \Omega_i = \sigma^2\begin{pmatrix} 1 & \rho & \cdots & \rho^{T-1} \\ \rho & 1 & \cdots & \rho^{T-2} \\ \vdots & \vdots & \ddots & \vdots \\ \rho^{T-1} & \rho^{T-2} & \cdots & 1 \end{pmatrix}$$

모든 개체에 동일한 $\rho$ 적용 → 추정 파라미터 **2개** ($\rho$, $\sigma^2$)

---

## 5. FGLS — 잔차로 $\hat{\Omega}$ 추정 후 대입

현실에서 $\Omega$ 모름 → 2단계 추정:

$$\underbrace{\text{OLS}}_{\text{1단계}} \rightarrow \hat{\epsilon}_{it} \rightarrow \hat{\Omega} \rightarrow \underbrace{\text{GLS}}_{\text{2단계}} \rightarrow \hat{\beta}_{FGLS}$$

$$\hat{\beta}_{FGLS} = (X^T\hat{\Omega}^{-1}X)^{-1}X^T\hat{\Omega}^{-1}Y$$

| 가정 | 추정 파라미터 | 추정 방법 |
|---|---|---|
| 개체 간 이분산 | $\hat{\sigma}_i^2$ N개 | $\hat{\sigma}_i^2 = \frac{1}{T}\sum_t\hat{\epsilon}_{it}^2$ |
| AR(1) 자기상관 | $\hat{\rho}$ 1개 | $\hat{\rho} = \frac{\sum\hat{\epsilon}_t\hat{\epsilon}_{t-1}}{\sum\hat{\epsilon}_{t-1}^2}$ |
| 둘 다 | N + 1개 | 위 두 방법 결합 |

> **FGLS = Feasible GLS** — $\Omega$ 몰라도 잔차로 추정해서 쓰는 GLS

### 패널에서 추정 가능성

| 가정 | 추정 파라미터 수 | 가능 여부 |
|---|---|---|
| 완전 자유 $\sigma_{it}^2$ | $\frac{NT(NT+1)}{2}$ | ✗ |
| 개체 간 이분산 $\sigma_i^2$ | N개 | ✓ |
| AR(1) 자기상관 | 2개 | ✓ |
| 둘 다 | N+2개 | ✓ |

> 개체 내 이분산 $\sigma_{it}^2$: 시점별 관측치가 1개라 분산 추정 불가

---

## 6. WLS — GLS의 특수 케이스 (대각 $\Omega$)

이분산만 있고 자기상관 없을 때 $\Omega$가 대각행렬:

$$\Omega = \begin{pmatrix} \sigma_1^2 & 0 & \cdots \\ 0 & \sigma_2^2 & \cdots \\ \vdots & \vdots & \ddots \end{pmatrix}, \quad W = \Omega^{-1} = \begin{pmatrix} \frac{1}{\sigma_1^2} & 0 & \cdots \\ 0 & \frac{1}{\sigma_2^2} & \cdots \\ \vdots & \vdots & \ddots \end{pmatrix}$$

$$\hat{\beta}_{WLS} = (X^TWX)^{-1}X^TWY$$

OLS 목적함수와 비교:

$$\text{OLS}: \min \sum_{t=1}^T \hat{\epsilon}_t^2 = \min \sum_{t=1}^T 1 \cdot \hat{\epsilon}_t^2$$

$$\text{WLS}: \min \sum_{t=1}^T \frac{1}{\sigma_t^2}\hat{\epsilon}_t^2$$

신뢰도 높은 관측치($\sigma_t^2$ 작음) → 가중치 $\frac{1}{\sigma_t^2}$ 크게 → 더 많이 반영

---

## 7. 가우스-마코프 정리 수학적 증명

> OLS 가정 성립 시, OLS는 모든 선형 불편 추정량 중 분산 최소

### 증명

임의의 선형 불편 추정량 $\tilde{\beta} = CY$에 대해:

$$C = (X^TX)^{-1}X^T + D \quad \text{(OLS에 } D \text{를 더한 형태)}$$

**불편 조건** $E(\tilde{\beta}) = \beta$ 적용:

$$CX = I \Rightarrow DX = 0$$

**분산 계산**:

$$Var(\tilde{\beta}) = \sigma^2 CC^T = \sigma^2((X^TX)^{-1}X^T + D)((X^TX)^{-1}X^T + D)^T$$

$DX = 0$ 조건 적용 후 전개:

$$= \sigma^2(X^TX)^{-1} + \sigma^2DD^T$$

$$= Var(\hat{\beta}_{OLS}) + \underbrace{\sigma^2DD^T}_{\geq 0}$$

$DD^T$는 양반정치행렬 → $\sigma^2DD^T \geq 0$

$$\therefore Var(\tilde{\beta}) \geq Var(\hat{\beta}_{OLS})$$

$D = 0$일 때만 등호 성립 → OLS와 동일한 추정량

---

## 한 줄 정리

- **OLS** — 오차 구조 무시, $\hat{\beta}$ 불편이지만 SE 틀림
- **GLS** — 오차 구조를 AR(1) 등 특정 형태로 **가정**하고, 파라미터($\rho$ 등)를 잔차로 추정해 $\Omega^{-1/2}$ 변환 후 OLS
- **FGLS** — $\Omega$를 완전히 특정할 수 없으므로 잔차로 $\hat{\Omega}$ 추정 후 대입하는 현실적 GLS

$$\text{OLS} \xrightarrow{\Omega \neq I,\ SE\ \text{과소추정}} \underbrace{\text{오차 구조 가정}}_{\text{AR(1) 등}} \xrightarrow{\hat{\rho}\ \text{추정}} \text{FGLS} \approx \text{GLS}$$

> $\hat{\beta}$는 살리고 오차 구조를 반영해 **SE를 교정하거나 추정 자체를 개선**하는 것이 GLS/FGLS의 핵심
