---
layout: post
title: "2. Confounder, Mediator, Collider"
date: 2026-05-26 01:00:00 +0900
categories: [Causal inference, Econometrics]
tags: [Confounder, Mediator, Collider, DAG, Selection Bias, Berkson Paradox, Causal Inference, 인과추론]
math: true
toc: true
---
 
> 인과추론에서 가장 흔한 실수는 변수를 잘못 다루는 것이다.  
> 통제해야 할 변수를 놓치거나, 통제하지 말아야 할 변수를 회귀식에 넣는 순간 추정이 무너진다.  
> Confounder, Mediator, Collider — 세 개념의 구조와 처리 원칙을 정확히 이해해야 한다.
 
---
 
## 0. 들어가기 전에 — "통제한다"는 것의 의미
 
본문에서 반복적으로 등장하는 **"통제(control)"** 라는 표현을 먼저 정확히 정의한다.
 
"$C$를 통제한다"는 것은 **$C$를 조건부로 만드는 모든 행위**를 포함한다.
 
---
 
### 0-1. 회귀식에 넣는다는 것의 의미
 
가장 전형적인 형태는 회귀식에 독립변수로 포함하는 것이다:
 
$$Y = \alpha + \beta D + \gamma C + \epsilon$$
 
여기서 $\hat{\beta}$의 수학적 의미는 **편미분**이다:
 
$$\hat{\beta} = \frac{\partial Y}{\partial D}\Bigg|_{C = \text{고정}}$$
 
> "$C$를 고정한 상태에서 $D$가 1 단위 증가할 때 $Y$의 변화"
 
이것이 핵심이다. $C$를 회귀식에 넣는 순간, 추정은 **"$C$의 값이 같은 집단 내에서 $D$와 $Y$의 관계"** 를 보게 된다.  
$C$는 더 이상 자유롭게 변하지 못한다 — 못 박혀 있다.
 
---
 
### 0-2. 왜 통제가 경로를 차단하는가
 
이 원리가 Mediator 분석에서 결정적으로 중요해진다. 구체적으로 보자.
 
$D \rightarrow M \rightarrow Y$ 경로가 존재한다고 하자.  
이 간접 경로가 작동하는 메커니즘은:
 
$$D \text{ 변함} \;\rightarrow\; M \text{ 변함} \;\rightarrow\; Y \text{ 변함}$$
 
그런데 회귀식에 $M$을 넣으면:
 
$$Y = \alpha + \beta D + \gamma M + \epsilon$$
 
추정은 **"$M$이 고정된 세계"** 안에서 이루어진다.  
$D$가 변해도 $M$은 변하지 않는다고 전제하는 순간, 간접 경로는 물리적으로 막힌다:
 
$$D \text{ 변함} \;\rightarrow\; M \text{ 변하지 않음 (고정)} \;\rightarrow\; Y \text{ 변화 없음}$$
 
$\hat{\beta}$에는 직접 경로 $D \rightarrow Y$의 효과만 남는다.  
이것이 "통제가 경로를 차단한다"는 말의 수학적 실체다.
 
반대로, $M$을 회귀식에서 빼면:
 
$$Y = \alpha + \beta D + \epsilon$$
 
$M$이 자유롭게 변할 수 있으므로 간접 경로가 열린다.  
$\hat{\beta}$는 직접 + 간접 경로를 **모두 합산한 총 효과**를 담는다.
 
요약하면:
 
| 모형 | $M$ 상태 | $\hat{\beta}$가 담는 것 |
|---|---|---|
| $Y = \alpha + \beta D + \epsilon$ | 자유롭게 변함 | 총 효과 (직접 + 간접) |
| $Y = \alpha + \beta D + \gamma M + \epsilon$ | 고정됨 | 직접 효과만 |
 
---
 
### 0-3. "통제"는 회귀식 포함에만 국한되지 않는다
 
그런데 "통제"는 회귀식 포함에만 국한되지 않는다.  
**$C$의 값을 고정(조건부)하는 모든 행위**가 통제다.
 
| 통제 방식 | 예시 | 의미 |
|---|---|---|
| **회귀식 포함** | $Y = \alpha + \beta D + \gamma C + \epsilon$ | $C$ 고정 후 $D$-$Y$ 관계 분석 |
| **샘플 필터링** | 입원 환자($C=1$)만 분석 | $C=1$인 세계로 분석 범위 제한 |
| **층화 분석** | $C$ 그룹별로 나눠서 분석 | 각 $C$ 값 안에서 비교 |
| **Matching** | $C$ 값이 비슷한 쌍끼리 매칭 | $C$ 분포를 균형화해 효과적으로 고정 |
 
회귀식에 명시적으로 넣지 않더라도, **샘플을 자르는 행위 자체가 특정 변수를 조건부로 거는 것**이다.  
이 점이 Collider 섹션에서 핵심이 된다 — 입원 환자만 분석하는 것은 "입원"이라는 변수를 통제한 것과 동일하다.
 
---

## 1. DAG 먼저

세 개념은 모두 **DAG(Directed Acyclic Graph)** 위에서 정의된다.  
DAG는 변수들 사이의 인과 방향을 화살표로 표현한 그래프다.

분석을 시작하기 전에 DAG를 먼저 그리는 것이 모든 실수를 예방하는 핵심 습관이다.  
변수를 회귀식에 추가하는 모든 결정은 DAG 위에서 정당화되어야 한다.

---

## 2. Confounder (교란변수)

### 구조

$$D \leftarrow C \rightarrow Y$$

```
    C
   ↗ ↘
D       Y
  (→)
```

$C$는 처치 $D$와 결과 $Y$ 모두의 **공통 원인**이다.  
$D \rightarrow Y$의 인과효과를 추정하고 싶은데, $C$가 두 변수를 동시에 움직이면서 효과를 오염시킨다.

### 왜 문제인가 — Selection Bias 수식 분해

단순 비교를 잠재적 결과 프레임워크로 분해하면:

$$\mathbb{E}[Y \mid D=1] - \mathbb{E}[Y \mid D=0]$$

$$= \underbrace{\mathbb{E}[Y(1) - Y(0) \mid D=1]}_{\text{처치효과 (원하는 것)}} + \underbrace{\mathbb{E}[Y(0) \mid D=1] - \mathbb{E}[Y(0) \mid D=0]}_{\text{Selection Bias (C로 인한 오염)}}$$

$C$를 통제하지 않으면 Selection Bias가 제거되지 않는다.

### 예시 — 교육과 임금

> **세팅**: 교육연수($D$)가 임금($Y$)에 미치는 효과를 추정한다.  
> **문제**: 가구 소득($C$)이 높을수록 교육을 더 많이 받고, 임금도 높다.

```
가구소득(C)
   ↗       ↘
교육연수(D) → 임금(Y)
```

**가구소득 통제 전** — 단순 비교:

| 집단 | 평균 교육연수 | 평균 임금 |
|---|---|---|
| 고소득 가정 출신 | 16년 | 400만원 |
| 저소득 가정 출신 | 12년 | 250만원 |

단순 비교 시 교육 1년당 효과: $(400 - 250) \div (16 - 12) = 37.5$만원

**가구소득 통제 후** — 동일 소득 집단 내 비교:

| 집단 | 교육연수 차이 | 임금 차이 |
|---|---|---|
| 고소득 가정 내 비교 | 1년 | 15만원 |
| 저소득 가정 내 비교 | 1년 | 13만원 |

가구소득을 통제하면 교육 1년당 효과가 **14만원** 수준으로 줄어든다.  
통제 전 37.5만원과의 차이(약 23.5만원)가 바로 **가구소득으로 인한 Selection Bias**다.  
$C$(가구소득)를 통제해야 비로소 순수한 교육 효과를 볼 수 있다.

### 처리 방법론

**원칙: 반드시 통제해야 한다.**

#### ① OLS / 회귀 통제

$$Y = \alpha + \beta D + \gamma C + \epsilon$$

$C$를 회귀식에 포함해 영향 제거.  
가정: 선형성, 모든 confounder가 관측 가능.

#### ② Matching / IPW

성향점수 $e(C) = P(D=1 \mid C)$를 추정한 뒤:

- **Matching**: 비슷한 $C$를 가진 처치군/통제군을 짝지어 비교
- **IPW**: 성향점수 역수로 가중 → 두 집단의 $C$ 분포를 균형화

$$\hat{ATE}^{IPW} = \frac{1}{N} \sum_i \left[\frac{D_i Y_i}{e(C_i)} - \frac{(1-D_i)Y_i}{1-e(C_i)}\right]$$

#### ③ DiD (Difference-in-Differences)

시간 불변 confounder는 개체 고정효과로 소거 가능:

$$\hat{\tau}^{DiD} = (\bar{Y}_{treat,post} - \bar{Y}_{treat,pre}) - (\bar{Y}_{control,post} - \bar{Y}_{control,pre})$$

Parallel Trend 가정 하에 시간 불변 교란변수 제거.

#### ④ IV / 2SLS

관측 불가능한 confounder가 있을 때:

$$\text{1단계: } D = \gamma_0 + \gamma_1 Z + \eta$$
$$\text{2단계: } Y = \alpha + \beta \hat{D} + \epsilon$$

도구변수 $Z$로 $D$의 외생적 변동만 추출 → 내생성 제거.

---

## 3. Mediator (매개변수)

### 구조

$$D \rightarrow M \rightarrow Y$$

```
D → M → Y
 ↘      ↗
  (직접 경로, 존재할 수도 있음)
```

$M$은 $D$와 $Y$ 사이의 **경로 위에 있는 변수**다.  
$D$의 효과가 $M$을 통해 전달되는 메커니즘을 담당한다.

### 총 효과의 분해

$$\underbrace{\text{Total Effect}}_{\text{총 효과}} = \underbrace{\text{Direct Effect (NDE)}}_{\text{직접 효과}} + \underbrace{\text{Indirect Effect (NIE)}}_{\text{간접 효과 (M 경유)}}$$

Mediator 분석의 목적은 총 효과를 이 두 경로로 **분리**하는 것이다.

### 예시 — 광고비와 매출

> **세팅**: 광고비($D$)가 매출($Y$)에 미치는 효과를 분석한다.  
> 광고가 브랜드 인지도($M$)를 높이고, 브랜드 인지도가 매출을 높이는 경로가 존재한다.

```
광고비(D) → 브랜드 인지도(M) → 매출(Y)
    ↘                           ↗
       (직접 효과: 즉각적 구매 유도)
```

- **직접 효과**: 광고 노출이 즉각적인 구매 행동으로 이어짐
- **간접 효과**: 광고 → 브랜드 인지도 상승 → 장기 매출 상승

두 경로를 분리하면 정책적 함의가 달라진다.  
단기 프로모션이 목적이면 직접 효과, 장기 브랜드 전략이 목적이면 간접 효과가 핵심이다.

### 처리 방법론

**원칙: 통제가 아니라 분해가 목적이다.**

#### ① Baron & Kenny (1986) — 전통적 방법

세 개의 회귀식을 순차 추정:

$$\text{Step 1: } Y = \alpha_1 + c \cdot D + \epsilon_1 \quad \rightarrow \text{Total Effect } (c)$$
$$\text{Step 2: } M = \alpha_2 + a \cdot D + \epsilon_2 \quad \rightarrow D \rightarrow M \text{ 경로 } (a)$$
$$\text{Step 3: } Y = \alpha_3 + c' \cdot D + b \cdot M + \epsilon_3 \quad \rightarrow \text{Direct Effect } (c')$$

$$\text{Indirect Effect} = a \times b = c - c'$$

**한계:**
- 선형성 가정에 의존
- $M$-$Y$ 사이 교란변수 존재 시 편향
- 인과해석이 엄밀하지 않음

#### ② Causal Mediation Analysis (Imai et al., 2010)

잠재적 결과 프레임워크로 엄밀하게 정의:

$$\text{ACME}(d) = \mathbb{E}[Y(d, M(1)) - Y(d, M(0))]$$

$$\text{ADE}(d) = \mathbb{E}[Y(1, M(d)) - Y(0, M(d))]$$

- **ACME (Average Causal Mediation Effect)**: $D$를 고정하고 $M$만 변화시킬 때의 효과 → 간접효과
- **ADE (Average Direct Effect)**: $M$을 고정하고 $D$만 변화시킬 때의 효과 → 직접효과

**Sequential Ignorability 가정:**

$$\{Y(d', m), M(d)\} \perp D \mid X$$
$$Y(d', m) \perp M(d) \mid D=d, X$$

R의 `mediation` 패키지, Python의 `causalml`로 추정 가능.

### ⚠️ 핵심 주의사항 — Mediator를 통제하면 안 되는 경우

총 효과가 목적이라면 $M$을 회귀식에 **절대 넣지 말아야** 한다.

$$Y = \alpha + \beta D + \epsilon \quad \leftarrow \text{총 효과 추정 (올바름)}$$
$$Y = \alpha + \beta D + \gamma M + \epsilon \quad \leftarrow M \text{통제 시 직접 효과만 남음, Collider Bias 위험}$$

$M$을 통제하면 $D \rightarrow M \rightarrow Y$ 경로를 막아버려 직접 효과만 남는다.  
더 큰 문제는 $M$-$Y$ 사이에 관측 불가능한 교란변수 $U$가 있을 경우 Collider Bias가 발생한다는 것이다.  
이는 3절 Collider에서 자세히 다룬다.

---

## 4. Collider (충돌변수)

### 구조

$$D \rightarrow C \leftarrow Y$$

```
D       Y
 ↘     ↙
    C
```

$C$는 $D$와 $Y$ 모두의 **공통 결과**다.  
Confounder와 화살표 방향이 정반대다.

### 핵심 역설

> **통제하지 않으면 괜찮은데, 통제하는 순간 bias가 생긴다.**

$D$와 $Y$가 원래 독립이더라도, $C$를 조건부로 걸면 **없던 상관관계가 인위적으로 생긴다.**

### 왜 이런 일이? — 직관

$C = D + Y$라고 단순화하자.

$C = 10$으로 **고정**하면:
- $D = 7$이면 → $Y$는 반드시 $3$
- $D = 3$이면 → $Y$는 반드시 $7$

$C$를 고정한 세계 안에서 $D$가 크면 $Y$가 작아야 하고, $D$가 작으면 $Y$가 커야 한다.  
원래 $D$와 $Y$는 독립이었는데, **$C$라는 공통 결과를 조건부로 건 순간 음의 상관이 생긴 것**이다.

이것이 Collider Bias의 본질이다.

---

### 예시 1 — Berkson's Paradox (버클슨 역설)

#### 세팅

```
당뇨(D) → 입원(C) ← 골절(Y)
```

> 일반 모집단에서 당뇨와 골절은 **서로 독립**이다.  
> 그런데 병원 입원 환자 데이터만 분석하면 **음의 상관**이 나온다.

#### 수치로 직접 확인

**일반 모집단 1,000명** (당뇨 20%, 골절 20%, 서로 독립):

| | 골절 O | 골절 X | 합계 |
|---|---|---|---|
| **당뇨 O** | 40 | 160 | 200 |
| **당뇨 X** | 160 | 640 | 800 |
| **합계** | 200 | 800 | 1,000 |

$$P(\text{당뇨} \mid \text{골절}) = \frac{40}{200} = 20\% = P(\text{당뇨}) \quad \Rightarrow \text{독립 확인}$$

**입원 조건**: 당뇨 또는 골절이 있으면 입원.  
입원 환자 추출: $40 + 160 + 160 = 360$명

| | 골절 O | 골절 X | 합계 |
|---|---|---|---|
| **당뇨 O** | 40 | 160 | 200 |
| **당뇨 X** | 160 | **0** | 160 |
| **합계** | 200 | 160 | 360 |

$$P(\text{당뇨} \mid \text{골절 O, 입원}) = \frac{40}{200} = 20\%$$
$$P(\text{당뇨} \mid \text{골절 X, 입원}) = \frac{160}{160} = 100\%$$

**입원 환자 안에서**: 골절이 없으면 반드시 당뇨 → **강한 음의 상관 발생**

#### 왜 이런 일이?

입원이라는 조건 안에서는 "왜 입원했는가?"가 항상 설명되어야 한다.

- 골절이 있다 → 당뇨 없어도 입원 이유 충분
- 골절이 없다 → **입원하려면 당뇨가 있어야** 함

입원 샘플 안에서 골절과 당뇨가 서로를 **대체하는 관계**로 묶여버린다.  
이것이 샘플 필터링이 통제의 일종인 이유다.  
회귀식에 $C$를 넣지 않았지만, **"입원 환자만 분석"이라는 행위 자체가 Collider를 조건부로 건 것**이다.

---

### 예시 2 — 합격자 역설

```
스펙(D) → 합격(C) ← 면접 실력(Y)
```

> 스펙과 면접 실력은 일반 모집단에서 무관하다.  
> 그런데 합격자만 분석하면?

합격하려면 스펙 **또는** 면접을 잘 봐야 한다.  
합격자 중에서:

- 스펙이 낮다 → 면접을 엄청 잘 봤을 것
- 스펙이 높다 → 면접을 못 봐도 합격 가능

→ **합격자 안에서 스펙과 면접 실력 간 인위적 음의 상관 발생**

합격자 데이터만 보면 "스펙이 좋을수록 면접 점수가 낮다"는 음의 상관이 나온다.  
실제로는 두 변수가 무관한데, 합격이라는 Collider를 샘플 기준으로 제한했기 때문이다.

---

### 예시 3 — 출판 편향 (Publication Bias)

```
효과 크기(D) → 출판(C) ← 유의성(Y)
```

> 표본 크기가 동일한 조건에서, 효과 크기와 p-value는 독립적일 수 있다.  
> 그런데 출판된 논문만 메타분석하면?

출판되려면 효과가 크거나 p-value가 작아야 한다.  
출판 샘플 안에서:

- 효과 크기가 작다 → p-value가 매우 작아야 출판됨
- 효과 크기가 크다 → p-value가 다소 커도 출판 가능

→ **출판 논문 안에서 효과 크기와 유의성 간 인위적 상관 발생**

"출판된 연구들을 보면 효과가 클수록 유의하지 않은 경향이 있다"는 결론은  
실제 관계가 아니라 **출판이라는 Collider를 조건부로 걸었기 때문**이다.

---

### 예시 4 — Mediator 통제로 인한 숨겨진 Collider

```
D → M → Y
    ↑
    U (unobserved, M-Y 사이 교란변수)
```

광고비-브랜드인지도-매출 예시로 구체화하면:

```
광고비(D) → 브랜드 인지도(M) → 매출(Y)
                    ↑
              경쟁사 활동(U, unobserved)
```

> 경쟁사가 공격적으로 마케팅을 하면 브랜드 인지도가 낮아지고, 매출도 낮아진다.  
> 즉 $U$(경쟁사 활동)는 $M$과 $Y$ 모두에 영향을 주는 교란변수다.

이 상황에서 $M$(브랜드 인지도)을 회귀식에 넣으면:

$$\text{매출} = \alpha + \beta \cdot \text{광고비} + \gamma \cdot \text{브랜드인지도} + \epsilon$$

$M$을 조건부로 거는 순간, $M$은 광고비($D$)와 경쟁사 활동($U$) 두 변수의 **공통 결과(Collider)**가 된다.  
$U$가 열리면서 광고비($D$)와 매출($Y$) 사이에 $U$를 통한 백도어 경로가 생기고,  
$\hat{\beta}$는 경쟁사 활동으로 인한 bias를 포함하게 된다.

---

### "통제"의 형태별 Collider Bias 발생 정리

| 통제 방식 | 예시 | Collider Bias 발생 |
|---|---|---|
| **회귀식 포함** | $C$를 독립변수로 추가 | ✅ 발생 |
| **샘플 필터링** | 입원 환자만 분석 ($C=1$) | ✅ 발생 |
| **층화 분석** | $C$ 그룹 안에서만 비교 | ✅ 발생 |
| **Mediator 통제** | $M$을 회귀식에 포함 (U 있을 때) | ✅ 발생 |
| **통제 안 함** | 전체 모집단 분석 | ❌ 발생 안 함 |

### 처리 방법

| 상황 | 해결책 |
|---|---|
| Collider를 모르고 통제 | DAG 먼저 그려서 화살표 방향 확인 |
| 샘플 선택 편향 | Heckman Selection Model, IPW 보정 |
| Mediator 통제로 인한 Collider | $M$ 제거 후 Causal Mediation Analysis 적용 |

---

## 5. 세 개념 최종 비교

### 구조 비교

| | **Confounder** | **Mediator** | **Collider** |
|---|---|---|---|
| DAG 구조 | $D \leftarrow C \rightarrow Y$ | $D \rightarrow M \rightarrow Y$ | $D \rightarrow C \leftarrow Y$ |
| 역할 | 공통 **원인** | 경로 **위** 변수 | 공통 **결과** |
| $D$와의 관계 | $D$에 영향을 **줌** | $D$에 의해 **영향받음** | $D$에 의해 **영향받음** |

### 처리 원칙 비교

| | **Confounder** | **Mediator** | **Collider** |
|---|---|---|---|
| 통제하면? | Bias 제거 ✅ | 경로 차단 + Collider Bias 위험 ⚠️ | Bias 생성 ❌ |
| 통제 안 하면? | Bias 존재 ❌ | 총효과만 추정됨 | 괜찮음 ✅ |
| 처리 원칙 | **반드시 통제** | **목적에 따라 분해** | **절대 통제 금지** |
| 주요 방법 | OLS, Matching, DiD, IV | Baron & Kenny, Causal Mediation | DAG 확인, Heckman |

---

## 6. 실전 체크리스트

```
1. DAG를 그린다
2. 각 변수의 화살표 방향을 확인한다
   - D와 Y의 공통 원인? → Confounder → 반드시 통제
   - D → Y 경로 위에 있음? → Mediator → 목적 확인 후 분해
   - D와 Y의 공통 결과? → Collider → 절대 통제 금지
3. "통제"의 형태를 점검한다
   - 회귀식 포함뿐 아니라 샘플 필터링, 층화도 통제임
   - 샘플 기준 자체가 Collider를 조건부로 걸고 있지 않은가?
4. 관측 불가능한 교란변수가 있는가?
   - 있으면 IV, Heckman 등 검토
5. Mediator를 통제할 경우, M-Y 사이 숨겨진 교란변수가 있는가?
   - 있으면 Collider Bias 발생 → Causal Mediation Analysis 적용
```

---

## 마치며

세 변수 유형의 핵심은 **화살표 방향**이다.  
Confounder와 Collider는 구조가 완전히 반대인데, 처리 원칙도 정반대다.  
"일단 다 통제하자"는 접근은 Collider Bias를 만들어내고, 샘플 선택 방식 자체도 통제의 일종임을 항상 인식해야 한다.

인과추론에서 변수를 추가하거나 샘플을 제한하는 모든 결정은 DAG 위에서 정당화되어야 한다.

---

## References

- Pearl, J. (2009). *Causality: Models, Reasoning, and Inference* (2nd ed.). Cambridge University Press.
- Imai, K., Keele, L., & Yamamoto, T. (2010). Identification, inference and sensitivity analysis for causal mediation effects. *Statistical Science*, 25(1), 51–71.
- Baron, R. M., & Kenny, D. A. (1986). The moderator–mediator variable distinction in social psychological research. *Journal of Personality and Social Psychology*, 51(6), 1173–1182.
- Berkson, J. (1946). Limitations of the application of fourfold table analysis to hospital data. *Biometrics Bulletin*, 2(3), 47–53.
- Angrist, J. D., & Pischke, J. S. (2009). *Mostly Harmless Econometrics*. Princeton University Press.
