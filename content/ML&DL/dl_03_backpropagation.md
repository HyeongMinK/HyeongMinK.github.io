---
title: 3. Backpropagation — 오차를 거꾸로 흘려보내는 법
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

## 파라미터를 업데이트하려면 뭐가 필요한가

Gradient descent의 업데이트 규칙은 항상 같다.

$$\Theta^{(l)} := \Theta^{(l)} - \alpha \frac{\partial J}{\partial \Theta^{(l)}}$$

결국 필요한 건 $\frac{\partial J}{\partial \Theta^{(l)}}$, 즉 **각 파라미터에 대한 cost의 편미분**이다.

단층인 로지스틱 회귀부터 시작해보자. cost function은:

$$J(\theta) = -\frac{1}{m}\sum_{i=1}^{m}\left[y^{(i)}\log h_\theta(x^{(i)}) + (1-y^{(i)})\log(1-h_\theta(x^{(i)}))\right]$$

$\theta_j$로 편미분하면 chain rule:

$$\frac{\partial J}{\partial \theta_j} = \frac{\partial J}{\partial h_\theta} \cdot \frac{\partial h_\theta}{\partial z} \cdot \frac{\partial z}{\partial \theta_j}$$

**① $\frac{\partial J}{\partial h_\theta}$:**

$$-\frac{y}{h_\theta} + \frac{1-y}{1-h_\theta}$$

**② $\frac{\partial h_\theta}{\partial z}$ — sigmoid 미분:**

$$h_\theta(1-h_\theta)$$

**③ $\frac{\partial z}{\partial \theta_j}$:**

$$z = \theta^T x = \theta_0 x_0 + \cdots + \theta_j x_j + \cdots \implies \frac{\partial z}{\partial \theta_j} = x_j$$

**세 개를 곱하면** sigmoid 미분이 cross-entropy 분모와 약분되어:

$$\frac{\partial J}{\partial \theta_j} = (h_\theta - y) \cdot x_j$$

레이어가 하나라서 chain rule 경로가 짧고, $x_j$가 바로 나왔다.

그런데 레이어가 여러 개면 어떻게 될까. $\Theta^{(1)}$의 gradient를 구하려면 $J \to z^{(4)} \to z^{(3)} \to z^{(2)} \to \Theta^{(1)}$ 전체 경로를 chain rule로 타야 한다. 레이어가 깊어질수록 경로가 길어지고, 같은 중간 계산이 파라미터마다 반복된다.

Backpropagation은 이 반복되는 중간 계산을 $\delta$로 묶어서 효율적으로 처리하는 알고리즘이다.

---

## δ — 중간 재료의 정체

$\Theta_{jk}^{(l)}$에 대한 gradient를 chain rule로 전개하면:

$$\frac{\partial J}{\partial \Theta_{jk}^{(l)}} = \frac{\partial J}{\partial z_j^{(l+1)}} \cdot \frac{\partial z_j^{(l+1)}}{\partial \Theta_{jk}^{(l)}}$$

forward propagation에서 $z_j^{(l+1)} = \sum_k \Theta_{jk}^{(l)} a_k^{(l)}$이므로 $\Theta_{jk}^{(l)}$로 편미분하면:

$$\frac{\partial z_j^{(l+1)}}{\partial \Theta_{jk}^{(l)}} = a_k^{(l)}$$

$\Theta_{jk}^{(l)}$는 $a_k^{(l)}$와 선형으로 곱해져 있으니 당연한 결과다. 대입하면:

$$\frac{\partial J}{\partial \Theta_{jk}^{(l)}} = \underbrace{\frac{\partial J}{\partial z_j^{(l+1)}}}_{\delta_j^{(l+1)}} \cdot a_k^{(l)}$$

여기서 $\delta$가 자연스럽게 등장한다.

### δ의 정의

$$\delta^{(l)} = \frac{\partial J}{\partial z^{(l)}}$$

$J$는 스칼라이고 $z^{(l)}$은 벡터이므로, $\delta^{(l)}$은 **gradient 벡터**다. layer $l$의 각 유닛의 가중합 $z$가 cost에 미치는 영향, 즉 각 유닛의 "오차 책임량"으로 해석할 수 있다.

$\delta$를 구해두면, 파라미터 gradient는 단순히:

$$\boxed{\frac{\partial J}{\partial \Theta^{(l)}} = \delta^{(l+1)} \cdot (a^{(l)})^T}$$

벡터 outer product 형태다. $\delta^{(l+1)}$은 열벡터 $(s_{l+1} \times 1)$, $(a^{(l)})^T$는 행벡터 $(1 \times s_l)$이므로 결과는 $(s_{l+1} \times s_l)$ 행렬 — $\Theta^{(l)}$과 차원이 정확히 일치한다.

즉 $\delta$는 파라미터를 직접 건드리는 값이 아니라, **각 레이어 파라미터의 gradient를 만들어내는 중간 재료**다. 모든 레이어의 $\delta$를 역방향으로 구해두면, 이전 레이어의 $a$와 곱해서 즉시 $\Theta$ 업데이트에 쓸 수 있다.

---

## 출력층 δ⁽⁴⁾ 유도

### cross-entropy + sigmoid (분류)

$z^{(4)}$는 $a^{(4)}$를 거쳐 $J$에 영향을 미치므로 chain rule:

$$\delta^{(4)} = \frac{\partial J}{\partial z^{(4)}} = \frac{\partial J}{\partial a^{(4)}} \cdot \frac{\partial a^{(4)}}{\partial z^{(4)}}$$

**① $\frac{\partial J}{\partial a^{(4)}}$ — cross-entropy를 $a^{(4)}$로 미분:**

$$J = -\left[y\log a^{(4)} + (1-y)\log(1-a^{(4)})\right]$$

$\log$의 미분 $\frac{d}{du}\log u = \frac{1}{u}$을 적용하면:

$$\frac{\partial J}{\partial a^{(4)}} = -\frac{y}{a^{(4)}} + \frac{1-y}{1-a^{(4)}}$$

**② $\frac{\partial a^{(4)}}{\partial z^{(4)}}$ — sigmoid 미분:**

$$a^{(4)} = \sigma(z^{(4)}), \quad \frac{\partial a^{(4)}}{\partial z^{(4)}} = a^{(4)}(1-a^{(4)})$$

**①②를 곱하면:**

$$\delta^{(4)} = \left(-\frac{y}{a^{(4)}} + \frac{1-y}{1-a^{(4)}}\right) \cdot a^{(4)}(1-a^{(4)})$$

각 항에 $a^{(4)}(1-a^{(4)})$를 분배:

$$= -y(1-a^{(4)}) + (1-y)a^{(4)}$$

전개:

$$= -y + ya^{(4)} + a^{(4)} - ya^{(4)}$$

$ya^{(4)}$ 상쇄:

$$\boxed{\delta^{(4)} = a^{(4)} - y}$$

sigmoid 미분 $a(1-a)$가 cross-entropy의 분모 $a^{(4)}$, $(1-a^{(4)})$와 정확히 약분되어 단순한 잔차 형태만 남는다. 로지스틱 회귀에서 gradient를 구할 때 $(h_\theta - y)$가 나왔던 것과 완전히 같은 구조다.

---

### MSE + identity (회귀)

출력층에 activation이 없으면 $a^{(4)} = z^{(4)}$이므로 $\frac{\partial a^{(4)}}{\partial z^{(4)}} = 1$:

$$\frac{\partial J}{\partial a^{(4)}} = \frac{\partial}{\partial a^{(4)}} \frac{1}{2}(a^{(4)} - y)^2 = a^{(4)} - y$$

$$\delta^{(4)} = (a^{(4)} - y) \cdot 1 = a^{(4)} - y$$

약분 없이 그냥 바로 나온다.

---

### 두 경우 비교와 canonical link

| | 분포 가정 | Loss | 출력 activation | $\delta^{(4)}$ |
|--|--|--|--|--|
| 분류 | Bernoulli | cross-entropy | sigmoid | $a^{(4)} - y$ |
| 회귀 | Gaussian | MSE | identity | $a^{(4)} - y$ |

형태가 동일하다. 이건 우연이 아니다.

sigmoid는 Bernoulli 분포의 **canonical link function**이고, cross-entropy는 그 분포의 negative log-likelihood다. 이 둘을 짝지으면 sigmoid의 $a(1-a)$가 cross-entropy 분모와 반드시 약분되도록 수학적으로 설계되어 있다. MSE + identity도 Gaussian 분포의 canonical link 조합으로 같은 이유다.

**canonical link를 쓰면 gradient는 항상 (예측 - 정답) 형태로 떨어진다.** 로지스틱 회귀에서도, 뉴럴넷 출력층에서도 동일하다.

반면 sigmoid 출력에 MSE를 쓰면:

$$\delta^{(4)} = (a^{(4)} - y) \cdot a^{(4)}(1-a^{(4)})$$

약분이 일어나지 않는다. 예측이 크게 틀렸을 때($a \approx 0$인데 $y=1$) $a(1-a) \approx 0$이라 gradient가 거의 0이 된다. 틀렸는데 수정이 거의 안 되는 **gradient vanishing**이 발생한다.

---

## hidden layer δ 역전파

출력층 $\delta^{(4)}$를 구했으면, 이걸 앞 레이어로 전달해야 한다. $\delta^{(3)}$를 구해보자.

$$\delta^{(3)} = \frac{\partial J}{\partial z^{(3)}}$$

$J$는 $z^{(3)}$에 직접 의존하지 않고, $z^{(4)}$를 거쳐 의존한다. 따라서 $z^{(4)}$의 각 성분을 중간 변수로 chain rule:

$$\frac{\partial J}{\partial z_k^{(3)}} = \sum_j \frac{\partial J}{\partial z_j^{(4)}} \cdot \frac{\partial z_j^{(4)}}{\partial z_k^{(3)}}$$

$\sum_j$는 layer 4의 모든 유닛 $j$에 대해 합산한다. $z_k^{(3)}$가 layer 4의 모든 유닛에 영향을 줄 수 있기 때문이다.

### $\frac{\partial z_j^{(4)}}{\partial z_k^{(3)}}$ 계산

forward propagation에서:

$$z_j^{(4)} = \sum_k \Theta_{jk}^{(3)} a_k^{(3)} = \sum_k \Theta_{jk}^{(3)} g(z_k^{(3)})$$

$z_k^{(3)}$로 편미분하면 $k$번째 항만 살아남고, chain rule로 $g'$이 붙는다:

$$\frac{\partial z_j^{(4)}}{\partial z_k^{(3)}} = \Theta_{jk}^{(3)} \cdot g'(z_k^{(3)})$$

### 대입 및 정리

$$\frac{\partial J}{\partial z_k^{(3)}} = \sum_j \underbrace{\frac{\partial J}{\partial z_j^{(4)}}}_{\delta_j^{(4)}} \cdot \Theta_{jk}^{(3)} \cdot g'(z_k^{(3)})$$

$g'(z_k^{(3)})$는 $j$와 무관하므로 밖으로:

$$= \left(\sum_j \Theta_{jk}^{(3)} \delta_j^{(4)}\right) \cdot g'(z_k^{(3)})$$

$\sum_j \Theta_{jk}^{(3)} \delta_j^{(4)}$를 보면, $\Theta_{jk}^{(3)}$에서 $k$는 고정, $j$에 대해 합산 — 이건 $\Theta^{(3)}$의 $k$번째 열과 $\delta^{(4)}$의 내적이다. 행렬 표기로 쓰면 $(\Theta^{(3)})^T \delta^{(4)}$의 $k$번째 원소다.

따라서 벡터 전체를 한 번에:

$$\boxed{\delta^{(3)} = (\Theta^{(3)})^T \delta^{(4)} \cdot* g'(z^{(3)})}$$

$\cdot*$는 element-wise 곱이다.

$g'(z^{(3)}) = a^{(3)} \cdot* (1 - a^{(3)})$ (sigmoid 미분)이므로 실제로는:

$$\delta^{(3)} = (\Theta^{(3)})^T \delta^{(4)} \cdot* a^{(3)} \cdot* (1 - a^{(3)})$$

### transpose가 붙는 이유

forward에서는 $z^{(4)} = \Theta^{(3)} a^{(3)}$으로 $\Theta^{(3)}$을 곱해서 앞으로 전달했다. 역방향으로 오차를 전달할 때는 그 역연산인 $(\Theta^{(3)})^T$가 붙는다. 가중치가 클수록 그 방향으로 오차가 더 크게 역전파되는 것이 자연스럽다.

---

## 전체 흐름

**역전파 (δ 계산):**

$$\underbrace{\delta^{(4)}}_{a^{(4)}-y} \xrightarrow{(\Theta^{(3)})^T,\; \cdot* g'} \delta^{(3)} \xrightarrow{(\Theta^{(2)})^T,\; \cdot* g'} \delta^{(2)}$$

input layer는 파라미터가 없으므로 $\delta^{(1)}$은 구하지 않는다.

**파라미터 gradient 계산:**

$$\frac{\partial J}{\partial \Theta^{(3)}} = \delta^{(4)} \cdot (a^{(3)})^T \quad [(s_4 \times 1)(1 \times s_3) = s_4 \times s_3]$$

$$\frac{\partial J}{\partial \Theta^{(2)}} = \delta^{(3)} \cdot (a^{(2)})^T \quad [(s_3 \times 1)(1 \times s_2) = s_3 \times s_2]$$

$$\frac{\partial J}{\partial \Theta^{(1)}} = \delta^{(2)} \cdot (a^{(1)})^T \quad [(s_2 \times 1)(1 \times s_1) = s_2 \times s_1]$$

**파라미터 업데이트:**

$$\Theta^{(l)} := \Theta^{(l)} - \alpha \frac{\partial J}{\partial \Theta^{(l)}}$$

$\delta$를 역방향으로 한 번씩만 계산해두면, 모든 레이어의 파라미터 gradient를 $\delta \cdot a^T$ 한 번으로 끝낼 수 있다. 이게 Backpropagation이 효율적인 이유다.

---

## 정리

Backpropagation의 핵심은 두 가지다.

첫째, $\delta^{(l)} = \frac{\partial J}{\partial z^{(l)}}$를 출력층에서 시작해 역방향으로 전파한다. 출력층의 $\delta$는 loss와 activation의 조합으로 결정되는데, canonical link를 쓰면 항상 $a - y$로 깔끔하게 떨어진다. 이건 로지스틱 회귀에서 gradient가 $(h_\theta - y) \cdot x_j$로 나왔던 것과 같은 수학적 구조다.

둘째, $\delta$는 파라미터를 직접 업데이트하지 않는다. $\frac{\partial J}{\partial \Theta^{(l)}} = \delta^{(l+1)} \cdot (a^{(l)})^T$를 통해 실제 gradient를 만들어내는 중간 재료다. 이 구조 덕분에 각 레이어의 $\delta$를 한 번씩만 계산하면 모든 파라미터의 gradient를 효율적으로 뽑아낼 수 있다.

---

→ **한줄요약:** Backpropagation은 $\delta = \frac{\partial J}{\partial z}$를 출력층에서 역방향으로 전파하고, $\frac{\partial J}{\partial \Theta} = \delta \cdot a^T$로 파라미터 gradient를 계산하는 과정이다. canonical link 조합에서 $\delta^{(4)} = a - y$로 떨어지는 것은 수학적으로 설계된 결과이며, 이 구조가 gradient 계산을 안정적이고 효율적으로 만든다.
