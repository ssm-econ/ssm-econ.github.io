---
title: "Information theory and statistical learning: Some intuition"
date: 2023-11-02
permalink: /posts/2023/10/infotheory/
excerpt: "Some basics of information theory and entropy. A small discussion on informativeness of signals"
tags:
  - estimation
  - information theory
  - bayesian inference
---


# Information and Entropy: A Bayesian Perspective

## 1. Introduction

Entropy, originally a concept from thermodynamics, is a measure of disorder or uncertainty in a system.  
In the context of information theory, information entropy quantifies the uncertainty inherent in a random variable or signal.  
Introduced by Claude Shannon in 1948, it forms the foundation of modern communication, data compression, and machine learning.

---

## 2. Motivation: Predictability and Surprise

Are all signals equally informative? Not quite.  
Consider two probability distributions:

**Case 1:**

```
p(x) = {
  0.99 if x = 0  
  0.01 if x = 1
}
```

**Case 2:**

```
q(x) = {
  0.5 if x = 0  
  0.5 if x = 1
}
```

- In the first case, the uncertainty is low.  
- In the second, each observation gives new information.  
- This illustrates that information is higher when uncertainty is higher.

**Shannon Entropy (for a discrete distribution p(x)):**

```
H(p) = − ∑ p(x) log p(x)
```

- Entropy is measured in **bits** (log base 2) or **nats** (log base e).  
- Example:
  - \( H(p) pprox 0.080 \) bits  
  - \( H(q) = 1.0 \) bit

---

## 3. Properties of Entropy

- **Non-negativity**: \( H(p) \geq 0 \)
- **Maximum entropy**: Occurs for a uniform distribution
- **Additivity**: \( H(X,Y) = H(X) + H(Y) \)
- **Concavity**: Entropy is concave in p

---

## 4. Maximum Entropy Principle

Given a finite set X, maximize:

```
H(p) = −∑ p(x) log p(x)
subject to ∑ p(x) = 1
```

Let \( f(t) = −t \log(t) \) — a **concave function**.

The maximum occurs at:

```
p(x_i) = 1/n  for all i
```

This is equivalent to choosing \( z_i \geq 0 \) such that:

```
∑ z_i = 1
```

Then solve:

```
max ∑ f(z_i)
```

Using Jensen’s Inequality:

```
∑ (1/n) f(z_i) ≤ f(∑ (1/n) z_i)
```

which leads to:

```
∑ (1/n) f(z_i) ≤ log(n)
```

Achieved when all \( z_i \) are equal:

```
p(x_i) = 1/n for all i
```

**Conclusion:**  
Maximum entropy occurs when all probabilities are equal — representing maximum uncertainty.

---

## 5. Differential Entropy

```
H(p) = − ∫ p(x) log p(x) dx
```

- **Can be negative**
- Not invariant under reparameterization
- Interpretation differs from discrete entropy
- Makes sense primarily when **comparing** distributions

---

## 6. KL Divergence and Related Concepts

**KL Divergence**:

```
D_KL(p ∥ q) = ∑ p(x) log [p(x) / q(x)]
```

Also:

```
D_KL(p ∥ q) = H(p, q) − H(p)
```

**Cross-Entropy**:

```
H(p, q) = −∑ p(x) log q(x)
```

**Reparameterization Invariance**:

```
D_KL(p(x) ∥ q(x)) = D_KL(p'(y) ∥ q'(y))
```

---

## 7. KL Divergence as Information Value

The **information gain** from a signal \( x \) is:

```
D_KL(p(θ | x) ∥ p(θ))
```

**Expected Information Gain**:

```
E_x~p(x) [D_KL(p(θ | x) ∥ p(θ))] = I(X; θ)
```

---

### Information Gain in Linear Regression

Suppose the model is:

```
y = ax + b + ε,    ε ∼ N(0, σ²)
```

with priors:

```
a ∼ N(μ_a, τ²_a),    b ∼ N(μ_b, τ²_b)
```

After observation \{x, y\}, update posterior:

**Likelihood**:

```
p(y | a, b) = N(y | ax + b, σ²)
```

**Posterior** is Gaussian:

```
p(a, b | x, y) = N(μ₁, Σ₁)
```

With:

```
Σ₁⁻¹ = Σ₀⁻¹ + (1/σ²) ΦᵀΦ  
μ₁ = Σ₁ [Σ₀⁻¹μ₀ + (1/σ²) Φᵀy]
```

Where:

```
Φ = [x  1]
```

**Information Gain (KL divergence)**:

```
D_KL(N(μ₁, Σ₁) ∥ N(μ₀, Σ₀)) =
1/2 [ tr(Σ₀⁻¹Σ₁) + (μ₀ − μ₁)ᵀΣ₀⁻¹(μ₀ − μ₁) − k + log(det Σ₀ / det Σ₁) ]
```

Where \( k = 2 \) (dimension of parameter vector).

---


Code snippet
```
# Re-run after code execution state reset
import numpy as np
import matplotlib.pyplot as plt

def kl_divergence_gaussians(mu0, Sigma0, mu1, Sigma1):
    """KL divergence between two multivariate Gaussians"""
    k = len(mu0)
    Sigma0_inv = np.linalg.inv(Sigma0)
    term1 = np.trace(Sigma0_inv @ Sigma1)
    term2 = (mu0 - mu1).T @ Sigma0_inv @ (mu0 - mu1)
    term3 = np.log(np.linalg.det(Sigma0) / np.linalg.det(Sigma1))
    return 0.5 * (term1 + term2 - k + term3)

# Fixed prior
mu0 = np.array([0.0, 0.0])
Sigma0 = np.diag([100.0, 100.0])

# Grid over x values and noise levels
x_vals = np.linspace(-5.0, 5.0, 100)
sigma_vals = np.linspace(0.1, 2.0, 100)
X, S = np.meshgrid(x_vals, sigma_vals)
KL = np.zeros_like(X)

# Compute information gain (KL divergence) for each (x, sigma) pair
for i in range(X.shape[0]):
    for j in range(X.shape[1]):
        x = X[i, j]
        sigma = S[i, j]
        Phi = np.array([[x, 1.0]])
        Sigma1_inv = np.linalg.inv(Sigma0) + (Phi.T @ Phi) / sigma**2
        Sigma1 = np.linalg.inv(Sigma1_inv)
        rhs = np.linalg.inv(Sigma0) @ mu0 + (Phi.T * 1.0 / sigma**2).flatten()  # Assume y = 1.0
        mu1 = Sigma1 @ rhs
        KL[i, j] = kl_divergence_gaussians(mu0, Sigma0, mu1, Sigma1)

# Plot the surface
fig = plt.figure(figsize=(8, 6),dpi=600)
cp = plt.contourf(X, S, KL, levels=30, cmap='viridis')
plt.colorbar(cp, label='KL Divergence (Information Gain)')
plt.xlabel("x (signal)")
plt.ylabel("σ (noise std dev)")
plt.title("Information Gain")
plt.tight_layout()
```

### Interpretation

- Higher KL divergence → More informative observation
- KL is higher when:
  - Posterior mean shifts significantly
  - Posterior variance reduces
- KL is low when:
  - Data supports prior (minimal update)

---
