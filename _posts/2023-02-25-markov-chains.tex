---
title: 'Markov Chains: A Gentle Introduction'
date: 2023-02-25
permalink: /posts/2023/02/markov-chain/
excerpt: "Markov Chains are the very foundation of stochastic processes. And they can open up a whole new way of thinking about problems in stochastic processes once we understand them."
tags:
  - probability
  - stochastic processes
---

# Markov Chains — A Gentle Introduction

> **Goal.** By the end, you should be able to define a (discrete-time) Markov chain, read and build a transition matrix, forecast with powers of that matrix, and explain when/why long-run behavior stabilizes.

> **Prereqs.** Basic probability (events, conditional probability), matrices at the level of multiplying compatible dimensions.

---

### What problem do Markov chains solve?
Many systems evolve step-by-step: today → tomorrow, minute $t$ → minute $t+1$. A **Markov chain** models such a system when the distribution of the next step depends **only** on the present state, not on the full history. This is the **Markov property**.

**Analogies you already know**
- **Weather:** If we only care about *Sunny* vs *Rainy*, tomorrow depends mostly on today.
- **Web browsing:** Your next page depends on the page you’re on.
- **Shopping loyalty:** Brand next month depends on the brand this month.

These are not laws of nature; they’re **useful approximations**—and useful approximations are where Markov chains shine.

---

## Formal setup
- **Time:** discrete steps $t=0,1,2,\dots$
- **States:** a set $S$ (finite or countable), e.g. $\{1,2,\dots,n\}$ or $\{\text{Sunny},\text{Rainy}\}$
- **Random variables:** $X_t\in S$ is the state at time $t$
- **Markov property:** for all states $i,j$ and all histories,
  $$
  \Pr(X_{t+1}=j\mid X_t=i, X_{t-1}=x_{t-1},\dots,X_0=x_0)=\Pr(X_{t+1}=j\mid X_t=i).
  $$
- **Time-homogeneous (assumed here):** the one-step probabilities don’t depend on $t$.

## Transition matrix
Collect the one-step probabilities in a matrix $P$ with entries
$$
P_{ij}=\Pr(X_{t+1}=j\mid X_t=i).
$$
We’ll use **row-stochastic** convention: rows sum to 1. If $s^{(t)}$ is the **row vector** of state probabilities at time $t$ (so $s^{(t)}_i=\Pr(X_t=i)$), then
$$
\boxed{s^{(t+1)} = s^{(t)}P}\qquad\text{and}\qquad s^{(t)}=s^{(0)}P^t.
$$
> *Pedantry that pays off:* If you prefer column vectors, transpose everything consistently. Mixing conventions causes errors.

**Chapman–Kolmogorov:** $P^{t+s}=P^tP^s$ and $(P^t)_{ij}=\Pr(X_t=j\mid X_0=i)$.

---

### A first example (two-state weather)
Let $S=\{\text{Sunny}=1,\;\text{Rainy}=2\}$ and
$$
P=\begin{bmatrix}
0.7 & 0.3\\
0.4 & 0.6
\end{bmatrix}.
$$
- If today is Sunny with certainty, $s^{(0)}=[1\;0]$. Then $s^{(1)}=s^{(0)}P=[0.7\;0.3]$: tomorrow is Sunny with prob. $0.7$, Rainy with prob. $0.3$.
- Two days out: $s^{(2)}=s^{(0)}P^2$. You can compute $P^2$ or multiply step-by-step.

**Stationary distribution.** A distribution $\pi$ with $\pi=\pi P$ (and entries nonnegative summing to 1) is called **stationary**. For the $P$ above,
$$
\pi_1=0.7\pi_1+0.4\pi_2 \;\Rightarrow\; 0.3\pi_1=0.4\pi_2 \;\Rightarrow\; 3\pi_1=4\pi_2.
$$
With $\pi_1+\pi_2=1$, solve to get $\pi=(4/7,\;3/7)\approx(0.5714,\;0.4286)$.

Interpretation: in the long run ~57% of days are Sunny and ~43% Rainy, regardless of the starting day (under mild conditions explained below).

---

## Long-run behavior: when do we converge?
For finite chains, three properties are central:
- **Irreducible:** from every state you can eventually reach every other state with positive probability.
- **Aperiodic:** the chain doesn’t get trapped in a deterministic cycle (formally, each state has period 1).
- **Ergodic theorem (informal):** if the chain is finite, irreducible, and aperiodic, then for any initial distribution $s^{(0)}$,
  $$
  s^{(t)} \to \pi\quad\text{as }t\to\infty,
  $$
  where $\pi$ is the unique stationary distribution.

**Why care?** It means the system “forgets” its starting point and settles into a stable mix of time spent in each state.

---

## Classifying states (bare essentials)
- **Absorbing:** $i$ is absorbing if $P_{ii}=1$. Once you enter, you never leave.
- **Transient vs. recurrent:** Starting from $i$, if the probability of ever returning to $i$ is $<1$, it’s **transient**; if it’s $1$, it’s **recurrent**. In any finite irreducible chain, all states are recurrent.
- **Communicating classes:** states partition into classes that can reach each other. A chain is **irreducible** iff there is a single communicating class.

> *Practical check:* If $P$ has an absorbing state, there can’t be a unique stationary distribution that attracts all starts; probability mass can get stuck.

---

## Building a Markov model from data (recipe)
1. **Choose states** so that the next step plausibly depends only on the current state. If the Markov property looks false, your state is probably too small; expand it.
2. **Estimate $P$:** for each state $i$, set
   $$
   \widehat{P}_{ij}=\frac{\#\,\text{of observed transitions }i\to j}{\#\,\text{of times you were in }i},\quad \text{then ensure each row sums to 1.}
   $$
3. **Validate:** hold out some sequences and compare forecasts $s^{(t)}=s^{(0)}\widehat{P}^t$ to observed frequencies.
4. **Analyze and simulate:** compute $\pi$ (solve $\pi=\pi\widehat{P}$), check irreducibility/aperiodicity, and simulate sample paths to build intuition.

---

## Examples from the real world
- **Queues (coffee shop):** State = number of people in line. Arrivals increment, services decrement. With “memoryless” arrival/service assumptions, this becomes a **birth–death chain**.
- **Board games:** State = square plus any special flags (e.g., “in jail”). Dice make transitions depend only on the current state once you model the rules properly.
- **Random surfer (PageRank):** State = webpage. Transition probabilities follow links plus occasional random jumps; $\pi$ ranks pages by long-run visit rate.
- **Credit ratings:** State = $\{\mathrm{AAA},\mathrm{AA},\mathrm{A},\dots,\mathrm{D}\}$. $P$ captures annual upgrade/downgrade/default probabilities.

---

### Pitfalls in applications (and how to dodge them)
- **Convention confusion:** Decide row-vs-column once. Here we use row vectors; rows of $P$ sum to 1; $s^{(t+1)}=s^{(t)}P$.
- **Time-homogeneity assumption:** If dynamics change (e.g., seasonality), either let $P$ depend on $t$ or add “season” to the state.
- **Hidden memory:** If the current state doesn’t capture key context (e.g., pressure/humidity for weather), expand the state definition.
- **Convergence myths:** Not all chains converge from every start; check irreducibility and aperiodicity.

---