---
title: 'Markov Chain Monte Carlo'
date: 2024-05-17
permalink: /posts/2024/05/mcmc/
excerpt: "Markov Chain Monte Carlo methods are widely used in Bayesian Econometrics. This article aims to clarify their theoretical basis and develop some intuition."
tags:
  - computational methods
  - bayesian econometrics
  - statistics
  - time series econometrics
---

## Markov Chain Monte Carlo

A class of statistical procedures based on designing Markov Chains with desired properties and generating samples from them.

**Big idea.** Sometimes we don’t observe a natural transition matrix—we *design* one to achieve specific goals (e.g., a desired stationary distribution, irreducibility, aperiodicity, fast mixing). This is the engine behind **Markov chain Monte Carlo (MCMC)**: build a chain whose stationary distribution is a target $\pi$ we want to sample from.

---

### Constructing chains with desired properties

- **Desired stationary distribution $\pi$.** A sufficient and convenient design rule is **detailed balance** (a.k.a. reversibility):
  $$
  \pi(i)\,P_{ij}=\pi(j)\,P_{ji}\quad\text{for all }i,j.
  $$
  Any $P$ that is stochastic (rows sum to 1), **irreducible**, **aperiodic**, and satisfies detailed balance w.r.t. $\pi$ has $\pi$ as a stationary distribution.

- **Aperiodicity by construction.** Add “laziness” (a self-loop) to any chain:
  $$
  P'=(1-\varepsilon)I+\varepsilon P,\quad 0<\varepsilon<1,
  $$
  which forces period 1 and preserves the same stationary distribution.

- **Irreducibility by construction.** Add a small “teleport” (PageRank-style) to guarantee every state can be reached:
  $$
  P'=(1-\alpha)P+\alpha\,\mathbf{1}\nu^\top,
  $$
  where $\nu$ is any probability vector over states (often uniform). If $P$ already has $\pi$-stationarity and $\nu=\pi$, then $\pi$ remains stationary for $P'$.

- **Uniform stationary distribution on graphs.** The simple random walk on an undirected graph (from $i$ pick a neighbor uniformly) is reversible with $\pi(i)\propto \deg(i)$. If you want $\pi$ uniform, make the graph regular or adjust transition probabilities accordingly.

---

### MCMC in one paragraph

We want to sample from a complicated $\pi$ (often known only up to a constant). **MCMC** constructs a Markov chain $X_0,X_1,\dots$ with stationary distribution $\pi$, runs it for a while (**burn-in**), and then treats the subsequent states as (dependent) draws from $\pi$. Key requirements: the chain must be **$\pi$-invariant**, **irreducible**, and **aperiodic** (then it’s ergodic and converges to $\pi$). Practical concerns: **mixing time**, **autocorrelation**, **effective sample size (ESS)**, and diagnostics (trace plots, acceptance rates, split-$\hat{R}$, etc.).

---

### Gibbs sampling

**When to use.** You can sample from each **full conditional** of a joint distribution $\pi(x_1,\dots,x_d)$, even if you can’t sample the joint directly.

**Algorithm (systematic-scan Gibbs).**
1. Start at $x^{(0)}=(x_1^{(0)},\dots,x_d^{(0)})$.
2. For $t=0,1,2,\dots$ update each coordinate in turn:
   $$
   x_1^{(t+1)}\sim \pi(x_1\mid x_2^{(t)},\dots,x_d^{(t)}),\quad
   x_2^{(t+1)}\sim \pi(x_2\mid x_1^{(t+1)},x_3^{(t)},\dots),\ \dots
   $$
   until $x_d^{(t+1)}$.

**Why it works.** The resulting transition kernel leaves $\pi$ invariant (it satisfies detailed balance with respect to $\pi$). Under mild conditions (irreducibility/aperiodicity), the chain converges to $\pi$.

**Variants.** *Random-scan Gibbs* picks coordinates at random; *blocked Gibbs* updates groups of variables together.

**Pitfalls.** Slow mixing when components are highly correlated; remedy via reparameterization or blocking.

---

### Metropolis–Hastings (MH)

**When to use.** You can propose a nearby state but can’t sample full conditionals easily.

**Ingredients.**
- A **proposal** $q(x,y)$ you can sample: propose $Y\sim q(x,\cdot)$ given current $x$.
- An **accept/reject** step with probability
  $$
  \alpha(x,y)=\min\!\left(1,\ \frac{\pi(y)\,q(y,x)}{\pi(x)\,q(x,y)}\right).
  $$

**Algorithm.**
1. Given $X_t=x$, draw $Y\sim q(x,\cdot)$.
2. Set
   $$
   X_{t+1}=\begin{cases}
   Y,& \text{with prob. }\alpha(x,Y),\\[4pt]
   x,& \text{otherwise (reject)}.
   \end{cases}
   $$
This satisfies detailed balance w.r.t. $\pi$ (hence $\pi$-invariance).

**Special cases.**
- **Random-walk Metropolis (RWM):** $q(x,y)=\mathcal{N}(y;x,\sigma^2 I)$ (symmetric), so $\alpha(x,y)=\min\!\big(1,\frac{\pi(y)}{\pi(x)}\big)$.
- **Independence sampler:** $q(x,y)=q(y)$, independent of $x$.

**Tuning.** Step size controls a trade-off: too small → high acceptance but slow exploration; too large → low acceptance. For high-dimensional RWM, a rule-of-thumb target acceptance is around $0.2$–$0.4$ (context dependent).

**Irreducibility & aperiodicity.** Ensure $q$ can reach the whole support of $\pi$ (possibly in multiple steps). A self-loop from rejection ensures aperiodicity.

---

### Metropolis-within-Gibbs (hybrid)

Gibbs-sampling is computationally efficient but requires that we be able to sample from conditional posteriors (which is easy if they have analytically closed expressions). MH sampler is quite general but converges quite slowly. However, these two can be combined in several applications, giving us the best of both worlds. 

Combine both: cycle through coordinates (or blocks) and, for those without tractable full conditionals, do a **Metropolis step** on that block. This retains $\pi$-invariance and often improves mixing.

---

### Tiny discrete example (MH on a 3-state target)

Target on $\{1,2,3\}$: $\pi\propto (1,2,1)$, i.e., $\pi(1)=\tfrac{1}{4},\ \pi(2)=\tfrac{1}{2},\ \pi(3)=\tfrac{1}{4}$.
Proposal: from $i$, propose a neighbor uniformly on $\{i-1,i+1\}$ when it exists; at the edges, propose the only neighbor.

- From $1$ propose $2$ and accept with
  $$
  \alpha(1,2)=\min\!\left(1,\frac{\pi(2)q(2,1)}{\pi(1)q(1,2)}\right)
  =\min\!\left(1,\frac{(1/2)\cdot(1/2)}{(1/4)\cdot 1}\right)=1.
  $$
- From $2$ propose $1$ or $3$ (each prob. $1/2$) and accept with
  $$
  \alpha(2,1)=\alpha(2,3)=\min\!\left(1,\frac{(1/4)\cdot 1}{(1/2)\cdot(1/2)}\right)=1.
  $$
So every proposal is accepted here; the resulting $P$ is just the simple neighbor-walk (with edge asymmetry) and has stationary distribution $\pi$. If any acceptance were $<1$, that leftover probability would stay as a self-loop, preserving aperiodicity.

---

### Practical checklist

- **Design for $\pi$-invariance:** detailed balance (often easiest) or a direct invariance proof.
- **Guarantee ergodicity:** ensure irreducibility (support coverage) and aperiodicity (self-loops or laziness).
- **Monitor mixing:** use trace plots, autocorrelations, ESS; adjust step sizes, blocking, or parameterization.
- **Burn-in & thinning:** discard early iterations before equilibrium; thinning is optional—prefer longer runs and ESS-based summaries.

> **Takeaway.** MCMC is “Markov-chain engineering”: you *build* a chain with exactly the stationary distribution you want. Gibbs and Metropolis–Hastings are the two fundamental construction kits; many modern samplers are clever hybrids built on these principles.
