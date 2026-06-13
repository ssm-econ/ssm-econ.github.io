---
title: 'Statistical filtering: An overview'
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

# Statistical Filtering and the Kalman Filter

Simply put, statistical filtering means extracting information regarding underlying hidden states from noisy signals (information), making use of the statistical properties of the signals and the dynamics of the state. To get a grasp of what all of this really means, let's begin by trying to understand how two imprecise signals can be combined to yield a measurement that *dominates* either of them.

---

## Problem Statement

Suppose you are on a lighthouse tracking the position of ships. You have access to two different sensors, A and B. However, one of the sensors (say sensor B) is old and has lost some of its precision. Do measurements from the old sensor carry any value once the new, more precise one is brought in? Intuition might suggest that the answer is no — if we have access to a less noisy device, why should we continue to use the older, noisier one? It would only pollute the measurement, right? But as we shall see, a key result in mathematical statistics shows that together they can do better than either one alone.

Let $x_t$ denote the true position of the ship. In learning theory, this is sometimes referred to as the *fundamental*.

The signals are:

$$
s_t^A = x_t + \varepsilon_t^A, \qquad s_t^B = x_t + \varepsilon_t^B,
$$

where

- $\varepsilon_t^A \sim \mathcal{N}(0, \sigma_A^2)$ and $\varepsilon_t^B \sim \mathcal{N}(0, \sigma_B^2)$,
- $\varepsilon_t^A$ and $\varepsilon_t^B$ are independent (both across sensors and over time),
- $x_t$ is the (unknown) true ship position at time $t$,
- $\sigma_A < \sigma_B$ (sensor A is more precise than sensor B).

We wish to combine $s_t^A$ and $s_t^B$ linearly such that the resulting estimate is *unbiased* (i.e., equal to $x_t$ in expectation). The optimal estimate weights each signal so the weights sum to 1:

$$
\hat{x}_t = \alpha s_t^A + (1-\alpha) s_t^B, \qquad 0 < \alpha < 1.
$$

Since $\mathbb{E}[\varepsilon_t^A] = \mathbb{E}[\varepsilon_t^B] = 0$, it follows that

$$
\mathbb{E}[\hat{x}_t] = \alpha \mathbb{E}[s_t^A] + (1-\alpha) \mathbb{E}[s_t^B] = x_t,
$$

so $\hat{x}_t$ is unbiased for $x_t$.

The variance of $\hat{x}_t$ is:

$$
\operatorname{Var}(\hat{x}_t) = \operatorname{Var}(\alpha \varepsilon_t^A + (1-\alpha) \varepsilon_t^B).
$$

Because $\varepsilon_t^A$ and $\varepsilon_t^B$ are independent, the cross-term vanishes:

$$
\operatorname{Var}(\hat{x}_t) = \alpha^2 \sigma_A^2 + (1-\alpha)^2 \sigma_B^2.
$$

We choose $\alpha$ to minimize this quantity. Differentiating with respect to $\alpha$, setting the derivative to zero, and solving gives

$$
\alpha^* = \frac{\sigma_B^2}{\sigma_A^2 + \sigma_B^2}, \qquad 1 - \alpha^* = \frac{\sigma_A^2}{\sigma_A^2 + \sigma_B^2}.
$$

Substituting back yields

$$
\operatorname{Var}(\hat{x}_t) = \frac{\sigma_A^2 \sigma_B^2}{\sigma_A^2 + \sigma_B^2}.
$$

Since

$$
\frac{\sigma_A^2 \sigma_B^2}{\sigma_A^2 + \sigma_B^2} < \min\{\sigma_A^2, \sigma_B^2\},
$$

the combined estimate $\hat{x}_t$ is strictly less noisy than using either sensor alone. **Even a less precise sensor can provide information and lead to more accurate estimates.**

---

## Intuition: Information Adds Up

**Independent errors mean independent information.** Even if $\sigma_B^2$ is large (sensor B is very noisy), it still offers information about $x_t$ that sensor A doesn't already have — because $\varepsilon_t^B$ is independent from $\varepsilon_t^A$. Of course, the larger $\sigma_B^2$, the smaller the marginal reduction in uncertainty.

**Weights are proportional to precision.** In the two-sensor case,

$$
\alpha^* = \frac{1/\sigma_A^2}{1/\sigma_A^2 + 1/\sigma_B^2}, \qquad 1-\alpha^* = \frac{1/\sigma_B^2}{1/\sigma_A^2 + 1/\sigma_B^2}.
$$

**Precision adds up.** The inverse of variance is called *precision*. When errors are independent, the precision of the combined estimate equals the sum of the individual precisions:

$$
\frac{1}{\operatorname{Var}(\hat{x}_t)} = \frac{1}{\sigma_A^2} + \frac{1}{\sigma_B^2}.
$$

With $n$ independent measurements, the optimal estimate and its variance are:

$$
\hat{x}_t = \frac{\sum_{i=1}^n \frac{1}{\sigma_i^2}\, s_t^{(i)}}{\sum_{i=1}^n \frac{1}{\sigma_i^2}}, \qquad \operatorname{Var}(\hat{x}_t) = \frac{1}{\sum_{i=1}^n 1/\sigma_i^2} < \min_i \sigma_i^2.
$$

---

## Dynamic Filtering

So far we have tackled **sensor fusion**, combining signals to estimate the position of a stationary ship. What happens when $x_t$ changes over time? At each step, we receive noisy signals about the current position and must track it continuously.

The *dynamics equation* governing the ship's position is:

$$
x_t = f x_{t-1} + w_t, \qquad w_t \sim \mathcal{N}(0, q^2).
$$

When $f < 1$ this is a mean-reverting process; when $f = 1$ it is a random walk. Large $q$ means the ship's position is subject to large random fluctuations (e.g., turbulent seas), making it inherently harder to forecast.

Observations follow:

$$
s_t = h x_t + \varepsilon_t, \qquad \varepsilon_t \sim \mathcal{N}(0, r^2),
$$

where $h$ maps the true position to the observation and $r^2$ is the observation noise variance.

Let the initial belief about position be $\mathcal{N}(x_{0|0}, p_{0|0})$. Denote the optimal estimate of position and variance conditional on signals $\{s_1, s_2, \dots, s_t\}$ as $\hat{x}_{t|t}$ and $p_{t|t}$. The **Kalman Filter**, first formalized by Rudolf Kalman in 1960, describes how to optimally update these beliefs.

### Kalman Filter: Core Update Equations

**Prediction.** Before observing $s_t$, form a prior using the dynamics:

$$
\hat{x}_{t|t-1} = f\, \hat{x}_{t-1|t-1}, \qquad p_{t|t-1} = f^2 p_{t-1|t-1} + q^2.
$$

**Update.** Combine the prior with the new signal $s_t$.

Kalman gain:

$$
K_t = \frac{h\, p_{t|t-1}}{h^2\, p_{t|t-1} + r^2}.
$$

State and covariance update:

$$
\hat{x}_{t|t} = \hat{x}_{t|t-1} + K_t\bigl(s_t - h\,\hat{x}_{t|t-1}\bigr), \qquad p_{t|t} = (1 - K_t h)\, p_{t|t-1}.
$$

---

## Dynamic Filtering with a Vector-Valued State

In many economic and time-series applications the object being estimated is not a scalar. Let the latent state be a vector $x_t \in \mathbb{R}^n$ evolving according to:

$$
x_t = F x_{t-1} + w_t, \qquad w_t \sim \mathcal{N}(0, Q),
$$

where $F \in \mathbb{R}^{n \times n}$ governs the state evolution, $Q \in \mathbb{R}^{n \times n}$ is the innovation covariance, and $w_t$ is independent over time.

The econometrician observes a vector $y_t \in \mathbb{R}^m$ of noisy measurements:

$$
y_t = H x_t + v_t, \qquad v_t \sim \mathcal{N}(0, R),
$$

where $H \in \mathbb{R}^{m \times n}$ maps the state into observables, $R \in \mathbb{R}^{m \times m}$ is the measurement noise covariance, and $v_t$ is independent of $w_s$ for all $s, t$.

For example, if $x_t$ contains a ship's true position and velocity, different sensors may observe different noisy linear combinations of these — one measuring position directly, another measuring velocity, and so on.

The Kalman filter gives the optimal linear Gaussian update of beliefs about $x_t$. At each date, beliefs are summarized by:

$$
x_t \mid \mathcal{I}_t \sim \mathcal{N}(\hat{x}_{t|t},\, P_{t|t}),
$$

where $\mathcal{I}_t$ denotes information available through date $t$.

---

## The Kalman Filter

### Prediction Step

Given beliefs after observing data through $t-1$,

$$
x_{t-1} \mid \mathcal{I}_{t-1} \sim \mathcal{N}(\hat{x}_{t-1|t-1},\, P_{t-1|t-1}),
$$

the one-step-ahead prediction is:

$$
\hat{x}_{t|t-1} = F\,\hat{x}_{t-1|t-1},
$$

with prediction covariance:

$$
P_{t|t-1} = F\, P_{t-1|t-1}\, F' + Q.
$$

The prior belief before observing $y_t$ is therefore $x_t \mid \mathcal{I}_{t-1} \sim \mathcal{N}(\hat{x}_{t|t-1},\, P_{t|t-1})$.

The two terms in $P_{t|t-1}$ have clear interpretations: $F P_{t-1|t-1} F'$ propagates past uncertainty forward, while $Q$ adds new uncertainty from the innovation $w_t$.

### Update Step

Once $y_t$ is observed, we compare it to what the model predicted. The predicted observation is:

$$
\hat{y}_{t|t-1} = H\,\hat{x}_{t|t-1}.
$$

The *innovation* (prediction error) is:

$$
\eta_t = y_t - H\,\hat{x}_{t|t-1},
$$

with covariance:

$$
S_t = H\, P_{t|t-1}\, H' + R.
$$

The first term, $H P_{t|t-1} H'$, reflects uncertainty about the latent state; $R$ reflects measurement error.

The **Kalman gain** is:

$$
K_t = P_{t|t-1}\, H' \left(H\, P_{t|t-1}\, H' + R\right)^{-1} = P_{t|t-1}\, H'\, S_t^{-1}.
$$

The updated state estimate and covariance are:

$$
\hat{x}_{t|t} = \hat{x}_{t|t-1} + K_t\, \eta_t,
$$

$$
P_{t|t} = (I - K_t H)\, P_{t|t-1}.
$$

A numerically more stable equivalent is the **Joseph form**:

$$
P_{t|t} = (I - K_t H)\, P_{t|t-1}\, (I - K_t H)' + K_t\, R\, K_t'.
$$

This form makes clear that posterior uncertainty depends both on the remaining prior uncertainty after updating and on the noise in the measurement itself.

---

## Deriving the Kalman Update

The update step follows directly from the Gaussian conditioning formula. Before seeing $y_t$:

$$
x_t \mid \mathcal{I}_{t-1} \sim \mathcal{N}(\hat{x}_{t|t-1},\, P_{t|t-1}).
$$

From the measurement equation $y_t = H x_t + v_t$ with $v_t \sim \mathcal{N}(0, R)$:

$$
y_t \mid \mathcal{I}_{t-1} \sim \mathcal{N}\!\left(H\,\hat{x}_{t|t-1},\; H\,P_{t|t-1}\,H' + R\right).
$$

The covariance between $x_t$ and $y_t$ (conditional on $\mathcal{I}_{t-1}$) is, since $v_t$ is independent of $x_t$:

$$
\operatorname{Cov}(x_t, y_t \mid \mathcal{I}_{t-1}) = P_{t|t-1}\, H'.
$$

For jointly Gaussian random variables, the conditional expectation is linear:

$$
\mathbb{E}[x_t \mid y_t, \mathcal{I}_{t-1}] = \hat{x}_{t|t-1} + \operatorname{Cov}(x_t, y_t)\,\operatorname{Var}(y_t)^{-1}\!\left(y_t - \mathbb{E}[y_t]\right).
$$

Substituting gives:

$$
\hat{x}_{t|t} = \hat{x}_{t|t-1} + P_{t|t-1}\, H' \left(H\, P_{t|t-1}\, H' + R\right)^{-1}\!\left(y_t - H\,\hat{x}_{t|t-1}\right),
$$

recovering the Kalman gain $K_t = P_{t|t-1}\, H' (H\, P_{t|t-1}\, H' + R)^{-1}$.

Similarly, the conditional covariance formula gives:

$$
P_{t|t} = P_{t|t-1} - P_{t|t-1}\, H' \left(H\, P_{t|t-1}\, H' + R\right)^{-1} H\, P_{t|t-1} = (I - K_t H)\, P_{t|t-1}.
$$

The Kalman filter is simply the Gaussian conditioning formula applied recursively over time.

---

## The Information Filter

The Kalman filter represents beliefs using means and covariance matrices. An equivalent representation uses precisions instead of variances.

Define the **precision matrix** and **information vector**:

$$
\Lambda_{t|t} = P_{t|t}^{-1}, \qquad \xi_{t|t} = \Lambda_{t|t}\,\hat{x}_{t|t}.
$$

The posterior mean is recovered as $\hat{x}_{t|t} = \Lambda_{t|t}^{-1}\,\xi_{t|t}$.

The key advantage of the information form is that measurement updates become **additive** — the dynamic analogue of the earlier static result: independent pieces of information add in precision space.

### Information-Form Update

Starting from the prior

$$
x_t \mid \mathcal{I}_{t-1} \sim \mathcal{N}(\hat{x}_{t|t-1},\, P_{t|t-1}),
$$

define $\Lambda_{t|t-1} = P_{t|t-1}^{-1}$ and $\xi_{t|t-1} = \Lambda_{t|t-1}\,\hat{x}_{t|t-1}$.

The prior density is proportional to:

$$
\exp\!\left[-\tfrac{1}{2}\,x_t'\,\Lambda_{t|t-1}\,x_t + x_t'\,\xi_{t|t-1}\right].
$$

The likelihood from $y_t \mid x_t \sim \mathcal{N}(H x_t, R)$ is proportional to:

$$
\exp\!\left[-\tfrac{1}{2}\,x_t'\,H'R^{-1}H\,x_t + x_t'\,H'R^{-1}y_t\right].
$$

Multiplying the prior and likelihood — adding the exponents — gives the posterior. Hence:

$$
\Lambda_{t|t} = \Lambda_{t|t-1} + H'R^{-1}H,
$$

$$
\xi_{t|t} = \xi_{t|t-1} + H'R^{-1}y_t.
$$

The posterior mean and covariance are recovered as:

$$
P_{t|t} = \Lambda_{t|t}^{-1}, \qquad \hat{x}_{t|t} = \Lambda_{t|t}^{-1}\,\xi_{t|t}.
$$

The central insight is:

$$
\underbrace{\Lambda_{t|t}}_{\text{posterior precision}} = \underbrace{\Lambda_{t|t-1}}_{\text{prior precision}} + \underbrace{H'R^{-1}H}_{\text{signal precision}}.
$$

A new measurement increases total information by $H'R^{-1}H$. If the measurement is very noisy, $R$ is large and $R^{-1}$ is small, so the information gain is small. If the measurement is precise, $R^{-1}$ is large, and the gain is large.

### Information-Form Prediction

The update step is especially clean in information form. The prediction step is less so, because the state transition introduces new uncertainty through $Q$.

Starting from the posterior objects $\hat{x}_{t-1|t-1}$ and $P_{t-1|t-1}$:

$$
\hat{x}_{t|t-1} = F\,\hat{x}_{t-1|t-1}, \qquad P_{t|t-1} = F\,P_{t-1|t-1}\,F' + Q.
$$

The predicted information objects are then:

$$
\Lambda_{t|t-1} = P_{t|t-1}^{-1} = \left(F\,\Lambda_{t-1|t-1}^{-1}\,F' + Q\right)^{-1},
$$

$$
\xi_{t|t-1} = \Lambda_{t|t-1}\,F\,\Lambda_{t-1|t-1}^{-1}\,\xi_{t-1|t-1}.
$$

The complete information filter is:

$$
\boxed{
\Lambda_{t|t-1} = \left(F\,\Lambda_{t-1|t-1}^{-1}\,F' + Q\right)^{-1}
}
$$

$$
\boxed{
\xi_{t|t-1} = \Lambda_{t|t-1}\,F\,\Lambda_{t-1|t-1}^{-1}\,\xi_{t-1|t-1}
}
$$

$$
\boxed{
\Lambda_{t|t} = \Lambda_{t|t-1} + H'R^{-1}H
}
$$

$$
\boxed{
\xi_{t|t} = \xi_{t|t-1} + H'R^{-1}y_t
}
$$

$$
\boxed{
\hat{x}_{t|t} = \Lambda_{t|t}^{-1}\,\xi_{t|t}
}
$$

---

## Interpretation

The ordinary Kalman filter updates beliefs by weighting the innovation $y_t - H\,\hat{x}_{t|t-1}$ with the Kalman gain:

$$
K_t = P_{t|t-1}\,H'\left(H\,P_{t|t-1}\,H' + R\right)^{-1}.
$$

When prior uncertainty $P_{t|t-1}$ is large, the filter places more weight on the new data. When measurement noise $R$ is large, the filter places less weight on the new data.

The information filter expresses the same logic in precision space. Each independent signal adds $H'R^{-1}H$ to the precision matrix and $H'R^{-1}y_t$ to the information vector. This is the dynamic version of the core lesson: **independent information adds up, but it adds up in precision space rather than variance space.**
