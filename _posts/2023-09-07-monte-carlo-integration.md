---
title: 'Monte-carlo integration'
date: 2023-09-07
permalink: /posts/2023/09/monte-carlo-integration/
excerpt: "When I first read about it, I was quite amazed that the task of approximating the area under a curve could be performed without really calculating areas at all. And instead, by rolling dice..."
tags:
  - state space methods
  - bayesian inference
---

# The arcanary of Monte-carlo integration 
When I first read about it, I was quite amazed that the task of approximating the area under a curve could be performed without really calculating areas at all. And instead, by rolling dice...

Monte-carlo integration is an ingenious concept. Its simplicity belies its genius. Consider the problem of evaluating the integral of the curve $f(x)$ over the interval $[a,b]$. Assuming that expectations are well-defined, 
$$\int_a^b f(x) dx = E[f(x)](b-a)$$

if x is uniformly distributed over $[a,b]$

We can employ this idea to great effect by combining it with another great theorem from statistics: The law of large numbers.

The law of large numbers simply states that the sample mean $\frac{1}{n}\sum_{i=1}^{n} X_i$ converges in probability to the population mean $\mu$ as $n$ approaches infinity.

All we need to do is randomly pick points in the interval such that they are uniformly distributed and evaluate the function over that set. To see this in action, you can use the following code snippet:


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

