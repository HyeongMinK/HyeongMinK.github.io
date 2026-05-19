---
title: 1. 패널 데이터의 오차 구조와 추정 전략 기초 
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
| 4 | $Cov(X_{it}, \epsilon_{it}) = 0$ | Unbiased | 추정량 편향 (내생성) |

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

## 3. 정규방정식에서 $Var(\hat{\beta})$ 유도 — 오차항 분산이 계수 분산으로 전파되는 구조

OLS 추정량과 오차항이 어떻게 연결되는지 먼저 유도한다. 이 식이 이후 모든 논의(GLS, FGLS, 로버스트 SE)의 출발점이 된다.

### 1단계 — 정규방정식에서 $\hat{\beta}$

$$X^TX\hat{\beta} = X^TY \quad\Rightarrow\quad \hat{\beta} = (X^TX)^{-1}X^TY$$

### 2단계 — $Y = X\beta + \epsilon$ 대입

$$\hat{\beta} = (X^TX)^{-1}X^T(X\beta + \epsilon) = \beta + (X^TX)^{-1}X^T\epsilon$$

### 3단계 — 분산 계산

$\beta$는 상수, $(X^TX)^{-1}X^T$는 X 조건부로 상수:

$$Var(\hat{\beta}) = Var\left((X^TX)^{-1}X^T\epsilon\right)$$

공식 $Var(AZ) = A \cdot Var(Z) \cdot A^T$ 적용 ($A = (X^TX)^{-1}X^T$):

$$Var(\hat{\beta}) = (X^TX)^{-1}X^T \cdot Var(\epsilon) \cdot X(X^TX)^{-1}$$

> $A^T = ((X^TX)^{-1}X^T)^T = X(X^TX)^{-1}$ 

> $(X^TX)$가 대칭이라 역행렬도 대칭

### 핵심 결과 — Sandwich 형태의 출처

$$\boxed{Var(\hat{\beta}) = (X^TX)^{-1}X^T\Omega X(X^TX)^{-1}}$$

$\hat{\beta}$가 $\epsilon$의 선형 함수이기 때문에 **오차항 분산이 그대로 계수 분산으로 전파**된다.

**경우 분석:**

- $Var(\epsilon) = \sigma^2 I$ (OLS 가정 성립) → $Var(\hat{\beta}) = \sigma^2(X^TX)^{-1}$ (익숙한 OLS 공식)
- $Var(\epsilon) = \sigma^2\Omega$ (일반) → Sandwich 형태 그대로 유지

> 가운데 $X^T\Omega X$를 **어떻게 처리하느냐**가 이후 GLS·FGLS·로버스트 SE 선택

| $\Omega$ 형태 | 문제 | 해결 |
|---|---|---|
| 대각, 원소 다름 | 이분산 | WLS |
| 비대각 원소 존재 | 자기상관 | GLS |
| RE 모형의 복합오차 $(u_i + e_{it})$ 구조 | 개체 내 시점 간 공분산 $\sigma_u^2 \neq 0$ | GLS |
| 둘 다 | 이분산 + 자기상관 | GLS |

---

## 4. 자기상관 → 분산 추정 오류 → t검정 신뢰 불가

$\hat{\beta}$는 가정 4만 성립하면 **불편** — 자기상관과 무관

$$E(\hat{\beta}) = \beta \quad \checkmark$$

그러나 OLS 분산 추정 공식은 $\Omega = I$를 전제한 것:

$$\widehat{Var(\hat{\beta})}_{OLS} = \hat{\sigma}^2(X^TX)^{-1}$$

실제 분산은 3장에서 유도한 Sandwich 형태:

$$Var(\hat{\beta}) = (X^TX)^{-1}X^T\Omega X(X^TX)^{-1}$$

$\Omega \neq I$이면 OLS SE **과소추정** → t통계량 과대 → 1종 오류 과다

$$t = \frac{\hat{\beta}}{SE(\hat{\beta})} \quad \leftarrow SE \text{ 가 틀림}$$

---

## 5. GLS — $\Omega^{-1/2}$ 변환으로 OLS 가정 복원

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

## 6. FGLS — 잔차로 $\hat{\Omega}$ 추정 후 대입

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

## 7. WLS — GLS의 특수 케이스 (대각 $\Omega$)

이분산만 있고 자기상관 없을 때 $\Omega$가 대각행렬:

$$\Omega = \begin{pmatrix} \sigma_1^2 & 0 & \cdots \\ 0 & \sigma_2^2 & \cdots \\ \vdots & \vdots & \ddots \end{pmatrix}, \quad W = \Omega^{-1} = \begin{pmatrix} \frac{1}{\sigma_1^2} & 0 & \cdots \\ 0 & \frac{1}{\sigma_2^2} & \cdots \\ \vdots & \vdots & \ddots \end{pmatrix}$$

$$\hat{\beta}_{WLS} = (X^TWX)^{-1}X^TWY$$

OLS 목적함수와 비교:

$$\text{OLS}: \min \sum_{t=1}^T \hat{\epsilon}_t^2 = \min \sum_{t=1}^T 1 \cdot \hat{\epsilon}_t^2$$

$$\text{WLS}: \min \sum_{t=1}^T \frac{1}{\sigma_t^2}\hat{\epsilon}_t^2$$

신뢰도 높은 관측치($\sigma_t^2$ 작음) → 가중치 $\frac{1}{\sigma_t^2}$ 크게 → 더 많이 반영

---

## 8. 가우스-마코프 정리 수학적 증명

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

## 9. 로버스트 추정 (Robust SE) — $\hat{\beta}$는 그대로, SE 공식만 교체

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

## 10. FGLS와 로버스트 SE의 $\Omega$ — 같은 기호, 다른 사용법

기호는 둘 다 $\Omega$지만, **이론적 대상은 같고 쓰는 방식만 다르다.**

### 진짜 $\Omega$의 의미

회귀모형 $y = X\beta + u$에서:

$$\Omega = Var(u \mid X)$$

오차항 전체의 분산-공분산 행렬. GLS·FGLS·로버스트 모두 동일한 대상을 가리킨다.

### 같은 $\hat{\Omega}$이라도 쓰는 방식이 다르다

이분산만 가정하면 둘 다 다음 형태를 쓸 수 있다:

$$\hat{\Omega} = \text{diag}(\hat{u}_1^2, \hat{u}_2^2, \ldots, \hat{u}_T^2)$$

| | FGLS | 로버스트 SE |
|---|---|---|
| $\hat{\Omega}$ 사용 | $\hat{\Omega}^{-1}$을 데이터에 직접 곱함 | $X^T\hat{\Omega}X$ 덩어리로 합산 후 SE 공식에 대입 |
| 영향 | $\hat{\beta}$ 자체가 바뀜 | $\hat{\beta}$ 그대로, SE만 바뀜 |
| 개별 $\hat{u}_i^2$의 신뢰도 | 매우 중요 | 합산에서 흡수되니 덜 중요 |

### 신뢰도 요구 수준의 차이

**로버스트 SE:**
- $\hat{u}_i^2$ 개별 값이 부정확해도 OK
- 어차피 $\sum_i \hat{u}_i^2 x_i x_i^T$로 **합산**되니까 평균적으로만 맞으면 됨
- 개별 부정확성이 합산에서 상쇄

**FGLS:**
- $\hat{\Omega}^{-1}$이 데이터에 **직접 곱해짐**
- 우연히 $\hat{u}_i^2$가 작으면 → $1/\hat{u}_i^2$ 폭발 → 그 관측치 가중치 폭증
- 그래서 단순 $\text{diag}(\hat{u}_i^2)$ 대신 보통 **구조 가정**을 더해 부드럽게 추정 ($\sigma_i^2 = \sigma^2 z_i^\gamma$ 등)

### 핵심 한 줄

| | 역할 |
|---|---|
| **GLS** | $\Omega$를 "알고 있다"고 가정 |
| **FGLS** | $\Omega$를 **회귀 재추정**에 씀 — 역행렬 취해서 데이터에 곱함 |
| **로버스트 SE** | $\Omega$를 **분산 계산**에만 씀 — 합산 덩어리로 SE 공식에만 끼움 |

**전략별 정리**
 
| 방법 | $\hat{\beta}$ | $\Omega$ 처리 | 효율성 | 안전성 |
|---|---|---|---|---|
| OLS | 불편, 비효율 | 무시 ($\Omega = I$ 전제) | 낮음 | $\hat{\beta}$ 불편 보장 |
| WLS | BLUE | 대각 $\Omega$ 알고 있음 (이분산만) | 높음 | 가정 필요 |
| GLS | BLUE | 완전한 $\Omega$ 알고 있음 | 최고 | 가정 필요 |
| FGLS | 점근적 BLUE | $\hat{\Omega}$ 잔차로 추정 후 대입 | 점근적 | 소표본 위험 |
| Robust SE | OLS와 동일 | 구조 가정 없이 잔차로 SE만 교정 | OLS와 동일 | 강건 |
 
---

## 11. FGLS가 현대 실무에서 덜 권장되는 이유

> 옛날엔 FGLS가 정석이었지만, 지금은 OLS + 로버스트 SE가 표준이다.

### ① 구조 가정의 위험

FGLS는 $\Omega$ 구조를 가정해야 한다 (AR(1), 모수적 이분산 등). 가정이 틀리면:

| | 가정 맞을 때 | 가정 틀릴 때 |
|---|---|---|
| OLS + 로버스트 SE | 효율성 낮지만 SE 올바름 | SE 여전히 올바름 |
| FGLS | BLUE | **$\hat{\beta}$ 편향 가능, SE도 틀림** |

OLS의 $\Omega = I$ 가정이 틀려도 $\hat{\beta}$는 불편이라 대가가 작지만, FGLS는 $\hat{\Omega}^{-1}$이 데이터에 직접 곱해지므로 가정 위반이 추정량에 직격탄이 된다.

### ② 소표본 불안정성

FGLS는 **점근적으로만** 효율적이다. 소표본에서는:
- $\hat{\Omega}$이 부정확
- 역행렬 취하면 불안정성 폭증
- $\hat{\beta}_{FGLS}$가 OLS보다 분산이 더 클 수 있음

### ③ $\hat{\Omega}^{-1}$ 폭발 위험

잔차 하나로 $\sigma_i^2$ 추정 후 역행렬:

$$\frac{1}{\hat{\sigma}_i^2} \rightarrow \text{잔차 우연히 작으면 폭발}$$

특정 관측치 가중치가 비정상적으로 커지면 $\hat{\beta}$가 그 관측치에 끌려간다.

### ④ 현대 응용계량경제학의 철학

> "약간의 효율성 손해는 감수해도, 가정 위반으로 인한 편향은 절대 피하자"

효율성보다 **일관성·강건성**을 우선시하는 흐름. 로버스트 SE는 이 철학에 부합한다.

### ⑤ OLS도 사실 구조 가정 — 근데 왜 FGLS만 문제냐

OLS의 $\Omega = I$도 엄밀히는 구조 가정이지만, **가정이 틀려도 $\hat{\beta}$가 불편**이라 대가가 작다. FGLS의 구조 가정은 **$\hat{\beta}$ 자체에 영향**을 미치므로 더 위험하다.

> **예외:** 패널 + 개체 간 이분산만 가정한 FGLS는 자유도 충분 + 가정 단순으로 비교적 안전. Stata `xtgls panel(hetero)`이 자주 쓰이는 이유.

---

## 한 줄 정리

- **OLS** — 오차 구조 무시, $\hat{\beta}$ 불편이지만 SE 틀림
- **GLS** — 오차 구조를 AR(1) 등 특정 형태로 **가정**하고, 파라미터($\rho$ 등)를 잔차로 추정해 $\Omega^{-1/2}$ 변환 후 OLS
- **FGLS** — $\Omega$를 완전히 특정할 수 없으므로 잔차로 $\hat{\Omega}$ 추정 후 대입하는 현실적 GLS
- **로버스트 SE** — $\hat{\beta}$는 OLS 그대로, $\Omega$ 구조 가정 없이 잔차로 분산만 직접 근사

$$\text{OLS} \xrightarrow{\Omega \neq I} \begin{cases} \text{구조 가정 가능} & \rightarrow \text{FGLS} \\ \text{구조 불확실} & \rightarrow \text{로버스트 SE} \end{cases}$$

> $\hat{\beta}$는 살리고 오차 구조를 반영해 **SE를 교정하거나 추정 자체를 개선**하는 것이 핵심 — 가정의 확신 정도에 따라 방법을 선택한다.