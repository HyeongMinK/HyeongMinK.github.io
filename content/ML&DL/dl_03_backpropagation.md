---
title: 3. Backpropagation
date: 2026-05-26
tags:
  - Backpropagation
  - Gradient Descent
  - Chain Rule
  - Delta
  - Canonical Link
---

# Backpropagation — 오차를 거꾸로 흘려보내는 법

앞선 글에서 forward propagation이 어떻게 이루어지는지 살펴봤다. 입력에서 출력까지 $z = \Theta a$, $a = g(z)$를 반복하며 예측값을 만들어내는 과정이었다.

그런데 예측값이 나왔다고 끝이 아니다. 틀렸으면 파라미터를 수정해야 한다. 그러려면 각 파라미터가 오차에 얼마나 기여했는지 알아야 하는데, 레이어가 여러 개면 이게 단순하지 않다. Backpropagation이 그 문제의 답이다.

---

## 단층에서 다층으로

단층 로지스틱 회귀에서 $\theta_j$에 대한 gradient는 chain rule로 바로 구할 수 있었다.

$$\frac{\partial J}{\partial \theta_j} = \frac{\partial J}{\partial h_\theta} \cdot \frac{\partial h_\theta}{\partial z} \cdot \frac{\partial z}{\partial \theta_j}$$

각각 계산하면:

$$\frac{\partial J}{\partial h_\theta} = -\frac{y}{h_\theta} + \frac{1-y}{1-h_\theta}, \quad \frac{\partial h_\theta}{\partial z} = h_\theta(1-h_\theta), \quad \frac{\partial z}{\partial \theta_j} = x_j$$

sigmoid 미분이 cross-entropy 분모와 약분되어:

$$\frac{\partial J}{\partial \theta_j} = (h_\theta - y) \cdot x_j$$

레이어가 하나라서 chain rule 경로가 짧고 깔끔하게 끝났다.

그런데 레이어가 여러 개면, $\Theta^{(1)}$의 gradient를 구하려면 $J \to z^{(4)} \to z^{(3)} \to z^{(2)} \to \Theta^{(1)}$ 전체 경로를 타야 한다. 더 문제는 이 중간 계산이 파라미터마다 반복된다는 것이다. Backpropagation은 이 반복을 $\delta$로 묶어서 한 번만 계산하는 알고리즘이다.

---

## δ — 중간 재료

$\Theta_{jk}^{(l)}$에 대한 gradient를 chain rule로 전개하면:

$$\frac{\partial J}{\partial \Theta_{jk}^{(l)}} = \frac{\partial J}{\partial z_j^{(l+1)}} \cdot \frac{\partial z_j^{(l+1)}}{\partial \Theta_{jk}^{(l)}}$$

forward propagation에서 $z_j^{(l+1)} = \sum_k \Theta_{jk}^{(l)} a_k^{(l)}$이므로:

$$\frac{\partial z_j^{(l+1)}}{\partial \Theta_{jk}^{(l)}} = a_k^{(l)}$$

대입하면:

$$\frac{\partial J}{\partial \Theta_{jk}^{(l)}} = \underbrace{\frac{\partial J}{\partial z_j^{(l+1)}}}_{\delta_j^{(l+1)}} \cdot a_k^{(l)}$$

여기서 $\delta$를 정의한다.

$$\boxed{\delta^{(l)} = \frac{\partial J}{\partial z^{(l)}}}$$

$J$는 스칼라, $z^{(l)}$은 벡터이므로 $\delta^{(l)}$은 gradient 벡터다. 각 유닛의 "오차 책임량"으로 해석할 수 있다.

$\delta$를 구해두면 파라미터 gradient는:

$$\frac{\partial J}{\partial \Theta^{(l)}} = \delta^{(l+1)} \cdot (a^{(l)})^T$$

outer product 형태가 나오는 이유는 스칼라 버전 $\frac{\partial J}{\partial \Theta_{jk}^{(l)}} = \delta_j^{(l+1)} \cdot a_k^{(l)}$을 모든 $(j, k)$에 대해 행렬로 쌓으면 자연스럽게 $\delta \cdot a^T$가 되기 때문이다. 차원도 $(s_{l+1} \times 1)(1 \times s_l) = s_{l+1} \times s_l$으로 $\Theta^{(l)}$과 정확히 일치한다.

$\delta$는 파라미터를 직접 건드리지 않는다. **각 레이어 $\delta$를 역방향으로 구해두면, 이전 레이어 $a$와 곱해서 바로 $\Theta$ gradient를 얻는 중간 재료**다.

---

## 출력층 δ⁽⁴⁾

$\delta^{(4)} = \frac{\partial J}{\partial z^{(4)}} = \frac{\partial J}{\partial a^{(4)}} \cdot \frac{\partial a^{(4)}}{\partial z^{(4)}}$

**분류 (cross-entropy + sigmoid):**

$$\frac{\partial J}{\partial a^{(4)}} = -\frac{y}{a^{(4)}} + \frac{1-y}{1-a^{(4)}}, \quad \frac{\partial a^{(4)}}{\partial z^{(4)}} = a^{(4)}(1-a^{(4)})$$

곱하면 sigmoid 미분이 cross-entropy 분모와 약분:

$$\delta^{(4)} = a^{(4)} - y$$

**회귀 (MSE + identity):**

$$\frac{\partial J}{\partial a^{(4)}} = a^{(4)} - y, \quad \frac{\partial a^{(4)}}{\partial z^{(4)}} = 1$$

$$\delta^{(4)} = a^{(4)} - y$$

두 경우 모두 $\delta^{(4)} = a^{(4)} - y$로 동일하다. 이건 우연이 아니라 canonical link 조합의 수학적 결과다. sigmoid는 Bernoulli의, identity는 Gaussian의 canonical link function이고, 각각의 loss(cross-entropy, MSE)와 짝지으면 gradient가 항상 $(예측 - 정답)$ 형태로 떨어진다.

반면 **sigmoid + MSE**를 쓰면:

$$\delta^{(4)} = (a^{(4)} - y) \cdot a^{(4)}(1-a^{(4)})$$

약분이 안 된다. 예측이 크게 틀려도 $a(1-a) \approx 0$이라 gradient가 거의 0 → **gradient vanishing**.

---

## hidden layer δ 역전파

$\delta^{(4)}$를 구했으면 앞 레이어로 전달한다.

$$\frac{\partial J}{\partial z_k^{(3)}} = \sum_j \frac{\partial J}{\partial z_j^{(4)}} \cdot \frac{\partial z_j^{(4)}}{\partial z_k^{(3)}}$$

forward에서 $z_j^{(4)} = \sum_k \Theta_{jk}^{(3)} g(z_k^{(3)})$이므로:

$$\frac{\partial z_j^{(4)}}{\partial z_k^{(3)}} = \Theta_{jk}^{(3)} \cdot g'(z_k^{(3)})$$

대입하고 $g'(z_k^{(3)})$를 밖으로:

$$\frac{\partial J}{\partial z_k^{(3)}} = \left(\sum_j \Theta_{jk}^{(3)} \delta_j^{(4)}\right) \cdot g'(z_k^{(3)})$$

$\sum_j \Theta_{jk}^{(3)} \delta_j^{(4)}$는 $(\Theta^{(3)})^T \delta^{(4)}$의 $k$번째 원소이므로:

$$\boxed{\delta^{(3)} = (\Theta^{(3)})^T \delta^{(4)} \cdot* g'(z^{(3)})}$$

transpose가 붙는 이유: forward에서 $\Theta^{(3)}$을 곱해서 앞으로 갔으니, 역방향에선 $(\Theta^{(3)})^T$로 되돌아온다.

### $g'(z^{(3)})$의 값

여기서 $g$는 sigmoid가 아니라 **hidden layer에 쓰는 activation function을 통칭하는 표기**다. 무엇을 쓰느냐에 따라 $g'$의 값이 달라진다.

| activation | $g'(z_k^{(3)})$ | 비고 |
|--|--|--|
| sigmoid | $a_k^{(3)}(1-a_k^{(3)})$ | forward의 $a^{(3)}$으로 바로 표현 가능 |
| ReLU | $1$ ($z>0$), $0$ ($z \leq 0$) | 양수 구간에서 항상 1 |
| tanh | $1 - a_k^{(3)^2}$ | sigmoid 대안 |

sigmoid를 쓰면 미분 최댓값이 0.25라서 레이어가 깊어질수록 $g'$가 역전파마다 곱해지며 gradient가 기하급수적으로 작아진다. 이것이 **gradient vanishing**이고, 요즘 hidden layer에 ReLU를 주로 쓰는 이유다.

이 구조는 loss function과 무관하다. 분류든 회귀든 hidden layer 역전파 공식은 동일하다. 차이는 오직 $\delta^{(4)}$의 값뿐이고, 그 값이 역전파되면서 아래 레이어 $\delta$에 영향을 준다.

---

## 전체 흐름

**Step 1 — 역전파로 δ 계산:**

$$\underbrace{\delta^{(4)}}_{a^{(4)}-y} \xrightarrow{(\Theta^{(3)})^T \cdot* g'} \delta^{(3)} \xrightarrow{(\Theta^{(2)})^T \cdot* g'} \delta^{(2)}$$

($\delta^{(1)}$은 input layer라 불필요)

**Step 2 — 파라미터 gradient 계산:**

$$\frac{\partial J}{\partial \Theta^{(l)}} = \delta^{(l+1)} \cdot (a^{(l)})^T$$

**Step 3 — 파라미터 업데이트:**

$$\Theta^{(l)} := \Theta^{(l)} - \alpha \frac{\partial J}{\partial \Theta^{(l)}}$$

---

## 정리

Backpropagation의 핵심은 두 가지다.

첫째, 출력층의 $\delta^{(4)} = a - y$는 loss + activation의 canonical link 조합에서 나오는 결과다. 분류(cross-entropy + sigmoid)와 회귀(MSE + identity) 모두 같은 형태가 나오고, 잘못된 조합(MSE + sigmoid)을 쓰면 gradient vanishing이 발생한다.

둘째, hidden layer의 역전파 구조 $\delta^{(l)} = (\Theta^{(l)})^T \delta^{(l+1)} \cdot* g'(z^{(l)})$는 loss function과 무관하다. $\delta$를 역방향으로 한 번씩만 구해두면, $\frac{\partial J}{\partial \Theta^{(l)}} = \delta^{(l+1)} \cdot (a^{(l)})^T$로 모든 파라미터 gradient를 효율적으로 뽑아낼 수 있다.

---

→ **한줄요약:** Backpropagation은 $\delta = \frac{\partial J}{\partial z}$를 출력층에서 역방향으로 전파해 $\frac{\partial J}{\partial \Theta} = \delta \cdot a^T$로 파라미터를 업데이트하는 알고리즘이며, 분류와 회귀의 차이는 출력층 $\delta^{(4)}$의 계산 방식 하나뿐이다.
