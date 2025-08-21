---
title: 'Monte-carlo methods'
date: 2023-09-07
permalink: /posts/2023/09/monte-carlo-methods/
excerpt: "The science of measurement by rolling dice."
tags:
  - computational methods
  - estimation and inference
---
# Monte Carlo methods
Monte Carlo methods are a broad class of computational algorithms that rely on repeated random sampling to obtain estimates of random variables.

*Measurement by rolling dice...*

The algorithms have proven extremely useful for situations where solutions to problems are desired but the problems are analytically intractable. If we have access to a randomizing device that can generate samples from a random distribution, we can often arrive at excellent estimates. Their true power is apparent when we use them in conjunction with Markov Chains utilising the powerful mathematical theory developed for them in order to simulate data.

## From integrals to expectations

Let $U \sim \mathrm{Unif}[a,b]$. We sample $n$ points from the uniform distribution between $a$ and $b$. We evaluate the function $f(.)$ at all $n$ points and calculate the _sample_ average. According to the law of large numbers, for any sequence of *iid* random variables $\{z_i\}_{i=1}^{n}$, define  the sample average$$\bar{Z}_n=\frac{\sum_{i=1}^{n}z_i}{n}$$
$\bar{Z}_n\to \mathbb{E}[Z]$ as $n\to\infty$.

If $x$ is a random variable with probability distribution function $p(x)$,  the expectation of a $f(x)$ is given by


$$ 
\mathbb{E}[f(U)] \;=\; \frac{1}{b-a}\int_a^b f(x)\,dx
\quad\Rightarrow\quad
\int_a^b f(x)\,dx \;=\; (b-a)\,\mathbb{E}[f(U)].
$$

This identity lets us turn integration into averaging: sample $U_i$ uniformly on $[a,b]$, evaluate $f(U_i)$. $U_i$'s are *independent, identically distributed* (iid) random variables. Since a function of a iid random variable is also a random variable, we have that:


Given *i.i.d*. $U_1,\dots,U_n \sim \mathrm{Unif}[a,b]$,
$$
\hat I_n \;=\; \frac{b-a}{n}\sum_{i=1}^n f(U_i)
$$
is an unbiased *estimator* of $\int_a^b f(x)dx$. Note that $\hat I_n$ is a random variable itself. It just has the property that its *asymptotic* (for large $n$) mean is equal to the quantity of interest. We have constructed it precisely to have this property.  

**Variance & error rate**
$$
\mathrm{Var}(\hat I_n)
= \frac{(b-a)^2}{n}\,\mathrm{Var}\!\big(f(U)\big),
 \text{so error } \sim \mathcal O(n^{-1/2}).
$$
Halving the error typically costs ~4× more samples.

**Confidence intervals (via CLT)**  
Let $y_i=f(U_i)$ $\bar y=\frac1n\sum y_i$, $s^2=\frac{1}{n-1}\sum(y_i-\bar y)^2$.  
MC standard error: $\text{SE} = (b-a)\, s/\sqrt{n}$.  
A ~95% CI is $\hat I_n \pm 1.96\,\text{SE}$.

---

## Minimal code 

```python
import numpy as np

def mc_integrate(f, a, b, n=10_000, seed=0):
    rng = np.random.default_rng(seed)
    u = rng.uniform(a, b, size=n)
    y = f(u)
    est = (b - a) * y.mean()
    se  = (b - a) * y.std(ddof=1) / np.sqrt(n)
    ci  = (est - 1.96*se, est + 1.96*se)
    return est, se, ci

# Example: ∫_0^1 x^2 dx = 1/3
f = lambda x: x**2
est, se, ci = mc_integrate(f, 0.0, 1.0, n=10000, seed=42)
print(f"Estimate = {est:.6f}, SE = {se:.6f}, 95% CI = [{ci[0]:.6f}, {ci[1]:.6f}]")

```python
import random

# Define the function to be integrated
def f(x):
    return x**2

# Define the integration limits
a = 0
b = 1

# Number of random samples
num_samples = 10000

# Initialize the sum
integral_sum = 0

# Perform Monte Carlo integration
for _ in range(num_samples):
    x = random.uniform(a, b)  # Generate a random point in the domain
    integral_sum += f(x)  # Add the function value at the random point

# Calculate the approximate integral
integral_approx = (b - a) * (integral_sum / num_samples)

print("Approximate integral:", integral_approx)

Approximate integral: 0.3353692778989746
```

**Problem:** Consider a slightly harder problem: We have a needle of unit length. It is randomly dropped on the floor in a square room with width 50 units. At the centre of the room is a circular rug with radius 25 units. What is the probability that the needle lies completely within the rug i.e no part of the needle touches the floor?

This is a fairly complicated problem to solve analytically.  But we can simulate the experiment and arrive at a pretty good estimate using Monte Carlo methods. The following code generates a sample of points where the mid-point of the needle lands from a uniform distribution. It then generates a sample of points denoting the orientation of the needle when it lands (which direction it points to) - from a uniform distribution over the range $[0,2\pi]$ (the range could be restricted to $[0,\pi]$ due to symmetry).

```python
import numpy as np

def mc_probability_needle_on_rug(n=1_000_000, seed=0, L=1.0, R=25.0, room_size=50.0):
    """
    Monte Carlo estimate of the probability that a needle of length L,
    dropped uniformly at random (uniform midpoint and orientation),
    lands entirely on a circular rug of radius R centered in a square room
    of side `room_size`.
    """
    rng = np.random.default_rng(seed)

    # Sample uniform midpoints in the room and orientations in [0, 2π)
    mx = rng.uniform(0.0, room_size, size=n)
    my = rng.uniform(0.0, room_size, size=n)
    theta = rng.uniform(0.0, 2*np.pi, size=n)

    # Half-length vector along the needle
    dx = (L / 2.0) * np.cos(theta)
    dy = (L / 2.0) * np.sin(theta)

    # Endpoints
    x1 = mx + dx;  y1 = my + dy
    x2 = mx - dx;  y2 = my - dy

    # Rug center (room center)
    cx = cy = room_size / 2.0

    # Check both endpoints are inside the rug (distance <= R)
    inside1 = (x1 - cx)**2 + (y1 - cy)**2 <= R**2
    inside2 = (x2 - cx)**2 + (y2 - cy)**2 <= R**2
    success = inside1 & inside2

    # Estimate, standard error, and ~95% CI
    p_hat = success.mean()
    se = np.sqrt(p_hat * (1.0 - p_hat) / n)
    ci = (p_hat - 1.96*se, p_hat + 1.96*se)
    return p_hat, se, ci

# Example run
if __name__ == "__main__":
    p_hat, se, ci = mc_probability_needle_on_rug(n=1_000_000, seed=7)
    print(f"Monte Carlo estimate: {p_hat:.6f}")
    print(f"Std. error: {se:.6f}")
    print(f"95% CI: [{ci[0]:.6f}, {ci[1]:.6f}]")
  ```  


## Importance sampling
So far we have restricted attention to samples generated from the uniform distribution. And for good reason! It's rather simple and intuitive. However we can do "better". We can reduce the variance of our estimates by sampling efficiently. 

Pick any pdf $p(x)$ on $[a,b]$ with $p(x)>0$ wherever $f(x)\neq 0$. Then re-write the integral using a "trick". 
$$
\int_a^b f(x)\,dx
=\int_a^b \frac{f(x)}{p(x)}\,p(x)\,dx
=\mathbb{E}_{X\sim p}\!\left[\frac{f(X)}{p(X)}\right].
$$

So an **unbiased estimator** of the integral is
$$
\hat I_n=\frac{1}{n}\sum_{i=1}^n \frac{f(X_i)}{p(X_i)},\qquad X_i\stackrel{\text{iid}}{\sim}p.
$$

Instead of evaluating $f(.)$ at the sampled points, we evaluate the function $f(.)/p(.)$ and calculate the sample average. 

Its variance is
$$
\mathrm{Var}(\hat I_n)
=\frac{1}{n}\!\left(\int_a^b \frac{f(x)^2}{p(x)}\,dx-\Big(\int_a^b f(x)\,dx\Big)^2\right).
$$

Compared to uniform, we can **lower this variance** by choosing $p$ that puts *more mass where $|f|$ is large* (and with tails heavy enough when the domain is unbounded).

---

## “Best” choice (and why)

The variance above is minimized (in shape) when
$$
p^*(x)\propto |f(x)|.
$$

If $f\ge 0$, then $p^*(x)\propto f(x)$ makes the weight $f(x)/p^*(x)$ a **constant**, i.e., **zero variance**.  
In practice you can’t use $p^*$ exactly because normalizing it requires the very integral you’re computing—but it tells you what *shape* to aim for.

---

## Efficient Monte Carlo sampling 

Let $f(x)=x^2$. Monte Carlo with points sampled from the uniform distribution estimates $\int_0^1 x^2\,dx=1/3$ with noise.  
If you sample from $p(x)=\mathrm{Beta}(3,1)$ whose pdf is $p(x)=3x^2\propto f(x)$, then
$$
\frac{f(x)}{p(x)}=\frac{x^2}{3x^2}=\frac{1}{3}\quad\text{(constant!)}
$$
so the estimate is exactly $1/3$ for **any** sample size—illustrating the zero-variance ideal.

```python
import numpy as np

def f(x): return x**2

# Importance sampling with p(x) = Beta(3,1)  => pdf p(x)=3x^2 on [0,1]
rng = np.random.default_rng(0)
x = rng.beta(3, 1, size=10_000)      # draw from p
w = f(x) / (3 * x**2)                # f(x)/p(x)
print("IS estimate:", w.mean())      # always ~ 1/3
``



