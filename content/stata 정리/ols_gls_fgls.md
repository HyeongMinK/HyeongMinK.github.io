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

### FGLS의 근본적 한계 — 순환 구조

OLS 잔차 $\hat{\epsilon}$은 **$\Omega = I$라고 가정하고 추정한 결과**다. 즉 틀린 가정 하에서 나온 근사값으로 $\hat{\rho}$, $\hat{\sigma}_i^2$을 추정하는 것이라 엄밀히는 정확하지 않다.

$$\underbrace{\hat{\beta}_{OLS}}_{\Omega=I \text{ 가정}} \rightarrow \underbrace{\hat{\epsilon}}_{\text{근사 잔차}} \rightarrow \underbrace{\hat{\rho},\ \hat{\sigma}_i^2}_{\text{편향된 추정}} \rightarrow \hat{\Omega} \rightarrow \hat{\beta}_{FGLS}$$

각 단계마다 오류가 누적되는 구조.

### 그럼에도 쓰는 이유 — 점근적 일치성

표본이 충분히 크면 ($N, T \rightarrow \infty$):

$$\hat{\epsilon} \rightarrow \epsilon, \quad \hat{\rho} \rightarrow \rho, \quad \hat{\Omega} \rightarrow \Omega$$

즉 **소표본에서는 편향이 있지만 대표본에서는 GLS에 수렴**한다.

| | 소표본 | 대표본 |
|---|---|---|
| GLS | BLUE | BLUE |
| FGLS | 편향 가능 | **점근적 BLUE** |

> FGLS는 엄밀히 **점근적으로(asymptotically) 효율적**이라고 표현한다 — 소표본에서는 보장 불가.

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

## 8. 로버스트 추정 (Robust SE) — $\hat{\beta}$는 그대로, SE 공식만 교체

GLS/FGLS는 $\Omega$의 구조를 가정하고 **추정 자체를 바꾸는** 방법이었다면, 로버스트 추정은 전혀 다른 접근이다.

> $\hat{\beta}_{OLS}$는 그대로 두고, **분산 추정 공식만 올바른 것으로 교체**한다.

### 왜 이런 접근이 필요한가

GLS/FGLS는 오차 구조를 특정 형태로 가정(AR(1) 등)해야 한다. 그 가정이 틀리면 오히려 더 나쁜 추정이 나올 수 있다. 로버스트 추정은 **$\Omega$의 구조를 전혀 가정하지 않고**, 잔차 자체로 분산을 직접 근사한다 — 가정이 틀릴 위험이 없다.

### Sandwich Estimator

OLS 분산 추정 공식은 $\Omega = I$를 전제하고 유도된 것:

$$\widehat{Var(\hat{\beta})}_{OLS} = \hat{\sigma}^2(X^TX)^{-1}$$

실제 분산 공식:

$$Var(\hat{\beta}) = (X^TX)^{-1} \underbrace{X^T\Omega X}_{\text{이걸 모름}} (X^TX)^{-1}$$

로버스트 추정은 모르는 $X^T\Omega X$ 부분을 잔차로 직접 근사:

$$\widehat{Var(\hat{\beta})}_{robust} = (X^TX)^{-1} \underbrace{\left(\sum_t \hat{\epsilon}_t^2 x_t x_t^T\right)}_{\hat{\Omega} \text{ 근사}} (X^TX)^{-1}$$

양쪽에 $(X^TX)^{-1}$이 끼어있는 모양 → **Sandwich Estimator**라고 부른다.

### 종류별 차이

**HC (Heteroskedasticity Consistent) — 이분산만**

$$\widehat{X^T\Omega X} = \sum_{t=1}^T \hat{\epsilon}_t^2 x_t x_t^T$$

각 관측치의 잔차 제곱으로 해당 시점의 분산을 근사. 자기상관은 고려하지 않아 인접 시점 간 항이 없다.

**HAC (Heteroskedasticity and Autocorrelation Consistent) — 이분산 + 자기상관**

$$\widehat{X^T\Omega X} = \sum_{t=1}^T \hat{\epsilon}_t^2 x_t x_t^T + \sum_{k=1}^{L} w_k \sum_t \hat{\epsilon}_t \hat{\epsilon}_{t-k}(x_t x_{t-k}^T + x_{t-k} x_t^T)$$

인접 시점($k$시점 떨어진) 잔차 간 공분산 항을 추가로 더한다. $w_k$는 시차가 멀어질수록 줄어드는 가중치(Bartlett kernel 등). $L$은 최대 시차(bandwidth).

- $k = 0$항만 쓰면 → HC와 동일
- $k > 0$항 추가 → 자기상관까지 흡수

**Clustered SE — 패널에서 개체 내 상관 전체 흡수**

$$\widehat{X^T\Omega X} = \sum_{i=1}^{N} \left(\sum_{t=1}^T x_{it}\hat{\epsilon}_{it}\right)\left(\sum_{t=1}^T x_{it}\hat{\epsilon}_{it}\right)^T$$

개체 i의 모든 시점 잔차를 **통째로 묶어서** 계산. AR(1)이든 AR(2)든 어떤 자기상관 구조든 상관없이 개체 내 상관을 가정 없이 흡수한다. 패널 데이터에서 가장 많이 쓰는 방식.

### GLS vs 로버스트 SE 비교

| | GLS / FGLS | 로버스트 SE |
|---|---|---|
| $\hat{\beta}$ | 재추정 (변환 후 OLS) | OLS 그대로 |
| $\Omega$ 처리 | 구조 가정 후 파라미터 추정 | 구조 가정 없이 잔차로 직접 근사 |
| 효율성 | 가정 맞으면 BLUE | OLS보다 효율성 낮음 |
| 가정 틀리면 | 더 나빠질 수 있음 | 최소한 SE는 올바름 |
| 실용성 | 오차 구조 명확할 때 | 구조 불확실하거나 보수적으로 갈 때 |

### 로버스트 SE가 고치는 것과 고치지 않는 것

$$t = \frac{\hat{\beta}_{OLS}}{SE(\hat{\beta}_{OLS})}$$

- 분자 $\hat{\beta}_{OLS}$ → **건드리지 않음**
- 분모 $SE(\hat{\beta}_{OLS})$ → **올바른 공식으로 교체**

즉 로버스트 SE는 추정의 효율성이나 $\hat{\beta}$의 정확성을 개선하는 게 아니라, **t검정이 제대로 작동하게 만드는 것**이 전부다.

| | 개선하는가 |
|---|---|
| $\hat{\beta}$ 불편성 | ✗ |
| $\hat{\beta}$ 효율성 | ✗ |
| 검정의 신뢰성 | **✓** |

### 핵심 트레이드오프

- GLS/FGLS: 오차 구조를 **알고 활용** → 추정 효율성 개선, 가정이 틀리면 위험
- 로버스트 SE: "추정은 포기하고 **검정만 제대로 하자**" → 효율성 포기, 대신 SE는 항상 올바름

> **실무에서는** 오차 구조 확신이 없으면 로버스트 SE를 쓰는 게 안전하다. Stata에서 `vce(robust)` 또는 `vce(cluster id)` 옵션 하나로 해결된다.

---

## 한 줄 정리

- **OLS** — 오차 구조 무시, $\hat{\beta}$ 불편이지만 SE 틀림
- **GLS** — 오차 구조를 AR(1) 등 특정 형태로 **가정**하고, 파라미터($\rho$ 등)를 잔차로 추정해 $\Omega^{-1/2}$ 변환 후 OLS
- **FGLS** — $\Omega$를 완전히 특정할 수 없으므로 잔차로 $\hat{\Omega}$ 추정 후 대입하는 현실적 GLS
- **로버스트 SE** — $\hat{\beta}$는 OLS 그대로, $\Omega$ 구조 가정 없이 잔차로 분산만 직접 근사

$$\text{OLS} \xrightarrow{\Omega \neq I} \begin{cases} \text{구조 가정 가능} & \rightarrow \text{FGLS} \\ \text{구조 불확실} & \rightarrow \text{로버스트 SE} \end{cases}$$

> $\hat{\beta}$는 살리고 오차 구조를 반영해 **SE를 교정하거나 추정 자체를 개선**하는 것이 핵심 — 가정의 확신 정도에 따라 방법을 선택한다.