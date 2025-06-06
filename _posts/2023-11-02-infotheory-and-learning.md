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

# Information Entropy

Entropy, originally a concept from thermodynamics, is a measure of disorder or uncertainty in a system.  In the context of information theory, information entropy quantifies the uncertainty inherent in a random variable or signal.  Introduced by Claude Shannon as part of his Master's Thesis at MIT in 1948, it forms the foundation of all modern statistical learning theory.

## Motivation: Predictability and Surprise

Are all signals equally informative? Not quite.  
Consider two probability distributions:

**Case 1:**

$$
p(x)= \begin{cases}
0.99 & \text{if } x=0 \\
0.01 & \text{if } x=1
\end{cases}
$$

**Case 2:**

$$
q(x)= \begin{cases}
0.5 & \text{if } x=0 \\
0.5 & \text{if } x=1
\end{cases}
$$

- In the first case, the uncertainty is low.  
- In the second, each observation gives new information.  
- This illustrates that information is higher when uncertainty is higher.

**Shannon Entropy (for a discrete distribution p(x)):**

$$
H(p) = -\sum_{x \in X} p(x) \log p(x)
$$

- Entropy is measured in **bits** (log base 2) or **nats** (log base e).  


## Properties of Entropy

- **Non-negativity**: $ H(p) \geq 0 $
- **Maximum entropy**: Occurs for a uniform distribution
- **Additivity**: $ H(X,Y) = H(X) + H(Y) $
- **Concavity**: Entropy is concave in p

---

## Maximum Entropy Principle


Given a finite set $ X $, maximize:
$$
H(p) = -\sum_{x \in X} p(x) \log p(x), \quad \sum_{x \in X} p(x) = 1
$$

Let $ f(t) = -t \log(t) $ (concave function). The maximum occurs at:
$$
p(x_i) = \frac{1}{n} \text{ for all } i
$$

Consider the following optimization problem:

$$max_{p(x)} H(p) = -\sum_{x\in X} p(x)\log p(x) $$ such that
$$\sum_{x\in X} p(x)=1$$


**Aliter**: 
$$f(t)=-t\log(t)$$ is a **concave** function.

We can write the equivalent maximization problem of choosing $z_i$ 

This is equivalent to choosing $z_i\geq0$ s.t 
$$\sum_{i=1}^n z_i=1$$ to solve

$$max_{z_i} \sum_{i=1}^n f(z_i) $$

\textbf{Concavity of f: Jensen's Inequality}

Using the definition of concavity, for any $0<\alpha<1$, 
$$f(\alpha x+(1-\alpha) y) \geq \alpha f(x) + (1-\alpha) f(y) \hspace{5 mm} \forall x,y \in Domain(f)$$

We can choose $\alpha_1=\alpha_2=...=\frac{1}{n}$

$$\sum_{i=1}^n \frac{1}{n}f(z_i) \leq f(\sum_{i=1}^n \frac{1}{n}z_i)$$
 

$$\sum_{i=1}^n \frac{1}{n}f(z_i) \leq \log(n)$$ which is achieved when $z_i$ are all equal i.e $p(x_i)=\frac{1}{n}$ for all i.

What this means is that the probability distribution that has the maximum entropy assigns equal probabilities to all elements in the set.

This is intuitive since if the probabilities are equal, there is maximum ex-ante uncertainty about the outcome.

---

## Differential Entropy

$$
H(p) = -\int_{-\infty}^{\infty} p(x) \log p(x) \, dx
$$

- **Can be negative**
- Not invariant under reparameterization
- Interpretation differs from discrete entropy
- Only meaningful when **comparing** distributions

---

## KL Divergence and Related Concepts

**KL Divergence**:


$$
D_{\text{KL}}(p \mid q) = \sum_{x \in X} p(x) \log \frac{p(x)}{q(x)}
$$

$$
D_{\text{KL}}(p \mid q) = H(p, q) - H(p)
$$

**Cross-Entropy**:

$$
H(p, q) = -\sum_{x \in X} p(x) \log q(x)
$$

**Reparameterization Invariance**:

$$
D_{\text{KL}}(p(x) \mid q(x)) = D_{\text{KL}}(p'(y) \mid q'(y))
$$

---

## KL Divergence as Information Value

The **information gain** from a signal $ x $ is:

$$
D_{\text{KL}}(p(\theta | x) \,\mid\, p(\theta))
$$
**Expected Information Gain**:

$$
\mathbb{E}_{x \sim p(x)} \left[ D_{\text{KL}}(p(\theta | x) \,\mid\, p(\theta)) \right] = I(X; \theta)
$$

---

## Information Gain in Linear Regression

Suppose the model is:

$$
y = ax + b + \epsilon, \quad \epsilon \sim \mathcal{N}(0, \sigma^2)
$$
with joint prior over:

$$
\begin{bmatrix}
a \\ b
\end{bmatrix} \sim \mathcal{N} \left(
\begin{bmatrix}
\mu_a \\ \mu_b
\end{bmatrix},
\begin{bmatrix}
\tau_a^2 & 0 \\
0 & \tau_b^2
\end{bmatrix}
\right)
$$

After observation  $\{x, y$ , update posterior:

**Likelihood**:

$$
p(y \mid a, b) = \mathcal{N}(y \mid ax + b, \sigma^2)
$$

**Posterior** is Gaussian:

$$
p(a, b \mid x, y) = \mathcal{N}(\mu_1, \Sigma_1)
$$

With:

$$
\Sigma_1^{-1} = \Sigma_0^{-1} + \frac{1}{\sigma^2} \Phi^\top \Phi
\quad \text{and} \quad
\mu_1 = \Sigma_1 \left( \Sigma_0^{-1} \mu_0 + \frac{1}{\sigma^2} \Phi^\top y \right)
$$

Where:

$ \Phi = [x \; 1] $

**Information Gain (KL divergence)**:

$$
D_{\text{KL}}\left( \mathcal{N}(\mu_1, \Sigma_1) \mid \mathcal{N}(\mu_0, \Sigma_0) \right)
= \frac{1}{2} \left[
\operatorname{tr}(\Sigma_0^{-1} \Sigma_1)
+ (\mu_0 - \mu_1)^T \Sigma_0^{-1} (\mu_0 - \mu_1)
- k + \log\left( \frac{\det \Sigma_0}{\det \Sigma_1} \right)
\right]
$$


Where $ k = 2 $ (dimension of parameter vector).



Code snippet for implementing the example
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

## Interpretation

  - Higher KL divergence → More informative observation
  - KL is higher when:
  - Posterior mean shifts significantly
  - Posterior variance reduces
  - KL is low when:
  - Data supports prior (minimal update)