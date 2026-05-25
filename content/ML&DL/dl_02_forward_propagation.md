---
title: 2. 뉴럴넷은 어떻게 계산하는가 — 표기법과 Forward Propagation
date: 2026-05-26
tags:
  - Neural Network
  - Forward Propagation
  - Vectorization
  - Notation
---

# 뉴럴넷은 어떻게 계산하는가 — 표기법과 Forward Propagation

지난 글에서 뉴럴넷의 진짜 목적이 representation learning이라는 걸 살펴봤다. 이번엔 그 계산이 구체적으로 어떻게 이루어지는지를 들여다본다. 수식이 처음엔 낯설어 보이지만, 구조를 파악하면 단순하다.

---

## 표기법부터

뉴럴넷을 다루다 보면 아래 두 표기가 계속 등장한다.

$$a_i^{(j)} = \text{layer } j \text{의 } i \text{번째 유닛의 activation 값}$$

$$\Theta^{(j)} = \text{layer } j \text{ → layer } j+1 \text{ 로의 가중치 행렬}$$

$a$는 activation function을 통과한 출력값이고, $\Theta$는 레이어 사이의 연결 강도다.

---

## 파라미터는 레이어 '사이'에 있다

중요한 점이 있다. 파라미터는 레이어 자체가 아니라 **레이어와 레이어 사이**에 존재한다.

```
layer 1 (input) →[Θ⁽¹⁾]→ layer 2 (hidden) →[Θ⁽²⁾]→ layer 3 (output)
     파라미터 X                  파라미터 X               파라미터 X
              파라미터 O                    파라미터 O
```

input layer는 데이터를 받아서 다음 레이어로 전달하는 역할만 한다. 학습되는 파라미터가 없다. 레이어가 $L$개면 파라미터 행렬은 $L-1$개다.

---

## 가중치 행렬의 차원

layer $j$에 $s_j$개의 유닛, layer $j+1$에 $s_{j+1}$개의 유닛이 있을 때:

$$\Theta^{(j)} \in \mathbb{R}^{s_{j+1} \times (s_j + 1)}$$

$+1$이 붙는 이유는 **bias unit** 때문이다. 각 레이어에는 항상 $x_0 = 1$ (또는 $a_0^{(j)} = 1$)이 추가된다.

아래 구조를 예시로 보면:
- layer 1: 입력 3개 ($x_1, x_2, x_3$) + bias $x_0 = 1$
- layer 2: hidden unit 3개
- layer 3: 출력 1개

$$\Theta^{(1)} \in \mathbb{R}^{3 \times 4}, \quad \Theta^{(2)} \in \mathbb{R}^{1 \times 4}$$

행이 다음 레이어 유닛 수, 열이 현재 레이어 유닛 수 + bias 1개다.

---

## Forward Propagation — 스칼라 표현

각 hidden unit은 이전 레이어 전체를 입력으로 받아 가중합을 구하고, activation function을 적용한다.

$$a_1^{(2)} = g(\Theta_{10}^{(1)}x_0 + \Theta_{11}^{(1)}x_1 + \Theta_{12}^{(1)}x_2 + \Theta_{13}^{(1)}x_3)$$
$$a_2^{(2)} = g(\Theta_{20}^{(1)}x_0 + \Theta_{21}^{(1)}x_1 + \Theta_{22}^{(1)}x_2 + \Theta_{23}^{(1)}x_3)$$
$$a_3^{(2)} = g(\Theta_{30}^{(1)}x_0 + \Theta_{31}^{(1)}x_1 + \Theta_{32}^{(1)}x_2 + \Theta_{33}^{(1)}x_3)$$

output layer:

$$h_\Theta(x) = a_1^{(3)} = g(\Theta_{10}^{(2)}a_0^{(2)} + \Theta_{11}^{(2)}a_1^{(2)} + \Theta_{12}^{(2)}a_2^{(2)} + \Theta_{13}^{(2)}a_3^{(2)})$$

---

## z로 분리하기

수식을 보면 $g(\cdots)$ 안의 가중합 부분이 반복된다. 이걸 $z$로 따로 빼두면 표현이 깔끔해진다.

$$z_i^{(j)} = \text{activation function 적용 직전의 가중합}$$
$$a_i^{(j)} = g(z_i^{(j)})$$

$z$는 선형 결합의 결과이고, $a$는 거기에 비선형 함수를 씌운 출력이다.

---

## Vectorized Implementation

스칼라 수식 세 개를 행렬 곱 하나로 묶을 수 있다.

$$x = \begin{bmatrix}x_0\\x_1\\x_2\\x_3\end{bmatrix} \in \mathbb{R}^4, \quad z^{(2)} = \begin{bmatrix}z_1^{(2)}\\z_2^{(2)}\\z_3^{(2)}\end{bmatrix} \in \mathbb{R}^3$$

**layer 1 → layer 2:**

$$z^{(2)} = \Theta^{(1)} x \quad \leftarrow [3 \times 4] \cdot [4 \times 1] = [3 \times 1]$$
$$a^{(2)} = g(z^{(2)}) \in \mathbb{R}^3$$

여기에 bias를 추가한다. ($a_0^{(2)} = 1$)

$$a^{(2)} \in \mathbb{R}^4 \quad \text{(bias 포함 후)}$$

**layer 2 → layer 3:**

$$z^{(3)} = \Theta^{(2)} a^{(2)} \quad \leftarrow [1 \times 4] \cdot [4 \times 1] = [1 \times 1]$$
$$h_\Theta(x) = a^{(3)} = g(z^{(3)})$$

---

## 전체 흐름

$$x \xrightarrow{\Theta^{(1)}} z^{(2)} \xrightarrow{g(\cdot)} a^{(2)} \xrightarrow{+\,a_0^{(2)}=1} \xrightarrow{\Theta^{(2)}} z^{(3)} \xrightarrow{g(\cdot)} h_\Theta(x)$$

각 레이어에서 하는 일은 딱 두 가지다.

1. **선형 변환**: $z^{(j+1)} = \Theta^{(j)} a^{(j)}$
2. **비선형 변환**: $a^{(j+1)} = g(z^{(j+1)})$

이 두 단계가 교대로 반복되면서 레이어가 깊어질수록 더 추상적인 표현이 만들어진다. 지난 글에서 말했던 representation learning이 이 구조를 통해 실현된다.

---

→ **한줄요약:** Forward propagation은 $z = \Theta a$ (선형) → $a = g(z)$ (비선형) 를 레이어마다 반복하는 것이고, 이를 행렬 곱으로 벡터화하면 $z^{(j+1)} = \Theta^{(j)} a^{(j)}$ 한 줄로 표현된다.
