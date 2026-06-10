---
layout: post
title: "1. ATE (Average Treatment Effect)"
date: 2026-05-26 00:00:00 +0900
categories: [Causal inference, Econometrics]
tags: [ATE, ATT, Potential Outcome, Selection Bias, Causal Inference, 인과추론]
math: true
toc: true
---

> 인과추론에서 가장 많이 등장하는 개념이 ATE다.  
> 단순한 상관관계 분석과 달리, ATE는 처치(treatment)가 결과(outcome)에 미치는 **진짜 인과효과**를 측정하려는 시도다.

---

## 1. 기본 설정 — Potential Outcome Framework

ATE를 이해하려면 먼저 **잠재적 결과(Potential Outcome)** 개념이 필요하다. (Rubin, 1974)

각 개체 $i$에 대해 두 가지 잠재적 결과를 가정한다:

- $Y_i(1)$: 개체 $i$가 처치를 **받았을 때**의 결과
- $Y_i(0)$: 개체 $i$가 처치를 **받지 않았을 때**의 결과

그러면 개체 $i$의 **개별 처치효과(ITE)**는:

$$\tau_i = Y_i(1) - Y_i(0)$$

### 근본적 문제 (Fundamental Problem of Causal Inference)

동일한 개체에 대해 $Y_i(1)$과 $Y_i(0)$을 **동시에 관측할 수 없다.**  
처치를 받으면 $Y_i(1)$만, 안 받으면 $Y_i(0)$만 관측된다.

따라서 $\tau_i$는 직접 계산 불가능 → ATE는 항상 **추정(estimation)**의 대상이다.

---

## 2. ATE 정의

$$ATE = \mathbb{E}[Y(1) - Y(0)]$$

전체 모집단에 걸쳐 처치 효과를 평균낸 값.  
"이 처치를 모집단 전체에 적용하면 평균적으로 효과가 얼마나 되는가?"

---

## 3. 관련 개념 비교

| 개념 | 정의 | 모집단 |
|---|---|---|
| **ATE** | $\mathbb{E}[Y(1) - Y(0)]$ | 전체 |
| **ATT** | $\mathbb{E}[Y(1) - Y(0) \mid D=1]$ | 처치받은 집단 |
| **ATU** | $\mathbb{E}[Y(1) - Y(0) \mid D=0]$ | 처치 안 받은 집단 |
| **LATE** | IV 추정량이 수렴하는 값 | Complier 집단 |

세 개념의 관계:

$$ATE = ATT \cdot P(D=1) + ATU \cdot P(D=0)$$

ATE는 ATT와 ATU의 **가중평균**이다.

---

## 4. 노테이션 풀어읽기

### 조건부 독립 표기

$$Y(1), Y(0) \perp D \mid X$$

| 기호 | 의미 |
|---|---|
| $\perp$ | 독립 (independent) |
| $\mid$ | 조건부 (given, ~을 알고 있을 때) |
| $D$ | 처치 여부 (1=받음, 0=안받음) |
| $X$ | 공변량 (관측 가능한 배경변수) |

**한국어 해석:**  
"$X$를 고정했을 때, 잠재적 결과 $Y(1), Y(0)$은 처치 배정 $D$와 무관하다."

즉, $X$가 같은 사람들끼리 비교하면 처치 배정이 사실상 랜덤처럼 작동한다는 뜻이다.

---

## 5. 왜 단순 비교는 틀리는가 — Selection Bias

처치군/통제군을 단순 비교하면:

$$\mathbb{E}[Y \mid D=1] - \mathbb{E}[Y \mid D=0]$$

이건 인과효과가 아니다. 분해하면:

$$= \underbrace{\mathbb{E}[Y(1) - Y(0) \mid D=1]}_{\text{처치효과 (우리가 원하는 것)}} + \underbrace{\mathbb{E}[Y(0) \mid D=1] - \mathbb{E}[Y(0) \mid D=0]}_{\text{Selection Bias (문제)}}$$

**Selection Bias**: 처치받은 집단이 **애초에 달랐기 때문에** 생기는 차이.

> 예) 공부 잘하는 학생이 과외를 더 받음  
> → 성적 차이가 과외 효과인지, 원래 능력 차이인지 구분 불가

$X$를 통제해 Selection Bias를 제거하는 것이 ATE 식별의 핵심이다.

---

## 6. 식별 조건 (Identification)

### ① Unconfoundedness (Ignorability)

$$Y(1), Y(0) \perp D \mid X$$

$X$를 통제하면 처치 배정이 랜덤과 동일하게 작동한다는 가정.  
이 가정 하에서:

$$\mathbb{E}[Y(0) \mid D=1, X] = \mathbb{E}[Y(0) \mid D=0, X]$$

통제군의 실제 결과로 처치군의 반사실을 대리할 수 있다.

### ② SUTVA

- 개체 간 spillover 없음
- 처치 버전이 하나뿐

### ③ Overlap (Common Support)

$$0 < P(D=1 \mid X) < 1$$

모든 $X$ 값에서 처치군/통제군 모두 존재해야 한다.

---

## 7. ATE vs ATT — 무엇이 다른가?

### 모집단의 차이

$$ATE = \mathbb{E}[Y(1) - Y(0)]$$

$$ATT = \mathbb{E}[Y(1) - Y(0) \mid D=1]$$

ATT의 $\mid D=1$은 **모집단 자체를 처치받은 집단으로 한정**한다는 뜻이다.  
그래서 ATT는 처치받은 사람들만의 세계에서의 평균 효과다.

### 필요한 반사실의 차이

**ATE**를 추정하려면 두 방향의 반사실이 모두 필요하다:

- $Y(0) \mid D=1$ : 처치군이 처치 안 받았다면? → 관측 불가
- $Y(1) \mid D=0$ : 통제군이 처치 받았다면? → 관측 불가

**ATT**를 추정하려면:

- $Y(1) \mid D=1$ : **직접 관측 가능** (처치군의 실제 결과)
- $Y(0) \mid D=1$ : 처치군이 처치 안 받았다면? → 관측 불가, 이것만 필요

$Y(1) \mid D=0$은 ATT 계산에 아예 등장하지 않는다.  
ATT는 모집단이 $D=1$로 한정되어 있기 때문에, 그 세계에 존재하지 않는 질문이다.

### 식별 조건의 차이

| | 필요한 독립 조건 |
|---|---|
| **ATE** | $Y(0) \perp D \mid X$ **AND** $Y(1) \perp D \mid X$ |
| **ATT** | $Y(0) \perp D \mid X$ **만** |

ATT는 $Y(1) \mid D=0$을 사용하지 않으니, 해당 가정이 불필요하다.  
→ **ATT의 가정이 더 약하고, 현실에서 더 자주 성립한다.**

---

## 8. 예시 — 직업훈련 프로그램의 임금 효과

**세팅:**
- $D$: 직업훈련 프로그램 참여 여부
- $Y$: 1년 후 월 임금 (만원)
- $X$: 학력, 나이, 이전 직무 경험

**현실 상황:**  
취업 의지가 강한 사람이 프로그램에 더 많이 참여한다.  
즉, **효과가 클 것 같은 사람이 처치를 선택**하는 경향이 있다.

| 집단 | 관측된 임금 | 반사실 임금 | 처치 효과 |
|---|---|---|---|
| 참여자 ($D=1$) | $\mathbb{E}[Y(1)\|D=1] = 280$ | $\mathbb{E}[Y(0)\|D=1] = 230$ | **ATT = 50** |
| 비참여자 ($D=0$) | $\mathbb{E}[Y(0)\|D=0] = 210$ | $\mathbb{E}[Y(1)\|D=0] = 240$ | ATU = 30 |

$$ATE = ATT \cdot P(D=1) + ATU \cdot P(D=0) = 50 \times 0.4 + 30 \times 0.6 = 38$$

**해석:**
- **ATT = 50**: 실제 참여자에게 프로그램은 평균 50만원 임금 상승 효과
- **ATU = 30**: 비참여자가 참여했다면 평균 30만원 효과였을 것
- **ATE = 38**: 전체 모집단 기준 평균 효과

ATT > ATU인 이유: **효과가 클 사람이 선택적으로 참여**했기 때문이다.

### 언제 무엇을 쓸까?

| 정책 질문 | 맞는 추정량 |
|---|---|
| "이 프로그램을 전 국민에게 확대하면?" | **ATE** |
| "이 프로그램이 실제 참여자에게 효과 있었나?" | **ATT** |
| "이 약을 실제 복용한 환자에게 효과 있었나?" | **ATT** |
| "이 약을 전체 환자에게 처방하면?" | **ATE** |

---

## 9. ATE 추정 방법 요약

| 방법 | 핵심 아이디어 | 주로 추정하는 것 |
|---|---|---|
| **RCT** | 완전 랜덤 배정 | ATE = ATT |
| **OLS** | $X$ 통제 후 회귀 | ATE (선형성 가정) |
| **Matching** | 비슷한 $X$ 끼리 매칭 | ATT |
| **IPW** | 성향점수 역수 가중 | ATE 또는 ATT |
| **DiD** | 처치 전후 + 집단 간 이중 차분 | ATT |
| **IV / 2SLS** | 도구변수 활용 | LATE (Complier 한정) |

---

## 마치며

ATE는 인과추론의 출발점이다.  
단순 비교가 왜 틀리는지(Selection Bias), 어떤 가정이 필요한지(Unconfoundedness), ATT와 어떻게 다른지(모집단의 차이)를 이해하면 DiD·IV·Matching 같은 방법론의 **왜**를 자연스럽게 따라갈 수 있다.

---

## References

- Rubin, D. B. (1974). Estimating causal effects of treatments in randomized and nonrandomized studies. *Journal of Educational Psychology*, 66(5), 688–701.
- Angrist, J. D., & Pischke, J. S. (2009). *Mostly Harmless Econometrics*. Princeton University Press.
- Imbens, G. W., & Wooldridge, J. M. (2009). Recent developments in the econometrics of program evaluation. *Journal of Economic Literature*, 47(1), 5–86.
