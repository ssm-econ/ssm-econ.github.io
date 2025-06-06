---
title: 'Filtering, Or why you shouldn't throw away a noisy sensor'
date: 2023-10-19
permalink: /posts/2023/10/filtering/
excerpt: "An introduction to filtering and sensor fusion"
tags:
  - filtering
  - kalman filter
  - bayesian inference
  - time series analysis
  - state space methods
---

# Filtering: Or why you shouldn't throw away a noisy sensor

Simply put, filtering means extracting information from a bunch of noisy signals. But the intuition behind it can be elusive. To illustrate the point, consider this problem.


## Problem Statement

Suppose you are on a lighthouse tracking the position of ships. You have access to two different sensors, A and B. However, one of the sensors (say sensor B) is old and has lost some of its precision. Should one still continue to use it? Intuition might suggest that the answer is no—if we have access to a better device, why should we continue to use the older one? A result in mathematical statistics shows that, together, they can do better than any of them alone.

Concretely, let the measured signals $s_t^A$ and $s_t^B$ be:

$$
s_t^A = x_t + \varepsilon_t^A, \qquad s_t^B = x_t + \varepsilon_t^B,
$$

where

- $\varepsilon_t^A \sim \mathcal{N}(0, \sigma_A^2)$ and $\varepsilon_t^B \sim \mathcal{N}(0, \sigma_B^2)$,
- $\varepsilon_t^A$ and $\varepsilon_t^B$ are independent (both across sensors and over time),
- $x_t$ is the (unknown) true ship position at time $t$,
- $\sigma_A < \sigma_B$ (that is, sensor A is more precise than sensor B).

We wish to combine $s_t^A$ and $s_t^B$ in a way that remains unbiased:

$$
s_t = \alpha s_t^A + (1-\alpha) s_t^B, \qquad 0 < \alpha < 1.
$$

Since $\mathbb{E}[\varepsilon_t^A] = \mathbb{E}[\varepsilon_t^B] = 0$, it is clear that

$$
\mathbb{E}[s_t] = \alpha \mathbb{E}[s_t^A] + (1-\alpha) \mathbb{E}[s_t^B] = x_t
$$

so $s_t$ is unbiased for $x_t$.

A measure of how “noisy” $s_t$ is can be defined via its variance:

$$
\operatorname{Var}(s_t) = \operatorname{Var}(\alpha \varepsilon_t^A + (1-\alpha) \varepsilon_t^B).
$$

Because $\varepsilon_t^A$ and $\varepsilon_t^B$ are independent with variances $\sigma_A^2$ and $\sigma_B^2$, respectively, the cross-term vanishes, and one obtains

$$
\operatorname{Var}(s_t) = \alpha^2 \sigma_A^2 + (1-\alpha)^2 \sigma_B^2.
$$

We choose $\alpha$ to minimize this quantity. Differentiating with respect to $\alpha$, setting the derivative to zero, and solving gives

$$
\alpha^* = \frac{\sigma_B^2}{\sigma_A^2 + \sigma_B^2}, \qquad 1 - \alpha^* = \frac{\sigma_A^2}{\sigma_A^2 + \sigma_B^2}.
$$

Substituting back yields

$$
\operatorname{Var}(s_t) = \frac{\sigma_A^2 \sigma_B^2}{\sigma_A^2 + \sigma_B^2}.
$$

Since

$$
\frac{\sigma_A^2 \sigma_B^2}{\sigma_A^2 + \sigma_B^2} < \min\{\sigma_A^2, \sigma_B^2\}
$$

the combined estimate $s_t$ is strictly less noisy than using either sensor alone. In other words, **even a less precise sensor can reduce overall variance of the measurement error and thus improve accuracy.**

---

## 1. Core Intuition: Independent Information Reduces Uncertainty

1. **Independent errors mean independent “information.”**
    - Even if $\sigma_B^2$ is large (i.e., B is very noisy), it still offers information about $x_t$ that sensor A doesn’t already have—because $\varepsilon_t^B$ is independent from $\varepsilon_t^A$.
    - Combining independent measurements as a “noisy messenger,” always shrinks the net variance below the smallest individual variance.

2. **The weighted‐average formula is weight ∝ 1/variance.**
    - In the two‐sensor case,
    $$
    \alpha^* = \frac{\sigma_B^2}{\sigma_A^2 + \sigma_B^2}
    $$
    and, equivalently,
    $$
    \alpha^* = \frac{1/\sigma_A^2}{1/\sigma_A^2 + 1/\sigma_B^2}, \qquad 1-\alpha^* = \frac{1/\sigma_B^2}{1/\sigma_A^2 + 1/\sigma_B^2}
    $$
    - Each sensor’s weight is its “precision” ($1/\sigma^2$) divided by the sum of all precisions.

3. **Generalization to $n$ sensors.**
    - With $n$ independent measurements,
    $$
    \hat x_t = \frac{\sum_{i=1}^n \frac{1}{\sigma_i^2} s_t^{(i)}}{\sum_{i=1}^n \frac{1}{\sigma_i^2}}, \qquad
    \operatorname{Var}(\hat x_t) = \frac{1}{\sum_{i=1}^n 1/\sigma_i^2} < \min_i \sigma_i^2.
    $$
    - Whenever $\sigma_i^2 < \infty$ and errors are independent, adding that measurement strictly lowers overall variance.

---

## 2. From Static Averaging to Dynamic Filtering

In many time‐series applications, $x_t$ itself evolves over time:

$$
x_t = F x_{t-1} + w_t, \qquad w_t \sim \mathcal{N}(0, Q)
$$

with measurements (e.g., two sensors):

$$
s_t^A = x_t + \varepsilon_t^A, \qquad \varepsilon_t^A \sim \mathcal{N}(0, \sigma_A^2) \\
s_t^B = x_t + \varepsilon_t^B, \qquad \varepsilon_t^B \sim \mathcal{N}(0, \sigma_B^2)
$$

### **Kalman Filter** (core update equations)

**Prediction:**

$$
\hat x_{t|t-1} = F \hat x_{t-1|t-1}, \qquad P_{t|t-1} = F P_{t-1|t-1} F^T + Q
$$

**Update:**

- Combine the two sensors as a precision-weighted average:
$$
\bar s_t = \frac{\frac{1}{\sigma_A^2} s_t^A + \frac{1}{\sigma_B^2} s_t^B}{\frac{1}{\sigma_A^2} + \frac{1}{\sigma_B^2}}
$$
with variance
$$
R_\text{agg} = \left( \frac{1}{\sigma_A^2} + \frac{1}{\sigma_B^2} \right)^{-1}
$$

- Kalman gain:
$$
K_t = \frac{P_{t|t-1}}{P_{t|t-1} + R_\text{agg}}
$$
- State/covariance update:
$$\hat x_{t|t} = \hat x_{t|t-1} + K_t (\bar s_t - \hat x_{t|t-1}), \qquad P_{t|t} = (1 - K_t) P_{t|t-1}$$
---

## 3. Why Even a Noisy Sensor Helps

- *Variance always drops:* At each time, the aggregate measurement’s variance is always smaller than that of the best individual sensor.
- *Precision compounds forward:* Each reduction of $P_{t|t}$ improves future predictions $P_{t+1|t}$.
- *Independence is crucial:* If errors are perfectly correlated, there is no gain, since the second signal provides little additional information in that case.

## 4. Beyond Two Sensors, Beyond Gaussian

- **Nonlinear, non-Gaussian:** As long as information is independent, combining measurements tightens posteriors (even if the formulas become more complex).
- **Many sensors:** Generalize by summing precisions. Variance is always
    $$
    \operatorname{Var}(\hat x_t) = \left( \sum_{i=1}^n 1/\sigma_i^2 \right)^{-1}
    $$
- **Observability:** Even a high-variance sensor may be essential if it sees a component of the state other sensors cannot. This could be a case where each signal is informative about a different part of the system.


