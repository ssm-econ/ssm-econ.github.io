---
title: "Time Series: Filtering, Or why you shouldn't throw away a noisy sensor"
date: 2023-10-19
permalink: /posts/2023/10/filtering/
excerpt: "An introduction to filtering"
tags:
  - filtering
  - kalman filter
  - bayesian inference
---


# Time Series: Filtering, and why you shouldn't throw away a noisy sensor
Simply put, filtering means extracting information from a bunch of noisy signals. 

But the intuition behind it can be elusive. To illustrate the point, consider this problem.
## Problem:
Suppose you are on a lighthouse tracking the position of ships. You have access to two different sensors A and B. However, one of the sensors (say sensor B) is old and has lost some of its precision. Should one still continue to use it? Intuition might suggest that the answer is no. If we have access to a better device, why should we continue to use this one? A result in mathematical statistics aids us and suggests that together, they can do better than any of them alone.


Say the measured signals $s_t^A$ and $s_t^B$ are given by:

$$s_t^A=x_t+\varepsilon_t^A$$


$$s_t^B=x_t+\varepsilon_t^B$$

Assuming that the errors are Gaussian and uncorrelated over time, which just means that the error in measurement today has nothing to do with the error yesterday. 

$$\varepsilon_t^A \sim Normal(0,\sigma_A^2)$$

$$\varepsilon_t^B \sim  Normal(0,\sigma_B^2)$$


And since the sensors are independent, so are the errors. i.e 


$$E\varepsilon_t^i| \varepsilon_t^j=E\varepsilon_t^i$$ 

Without loss of generality, let's say that sensor A is more precise i.e 

$$\sigma_A < \sigma_B$$


Let's say we combine the signal linearly such that the resulting signal remains unbiased:

$$s_t=\alpha s_t^A + (1-\alpha) s_t^B$$

where $0<\alpha<1$

Clearly, 
$$E (s_t)=x_t$$

A measure of how noisy this new signal is is its variance, defined as

$$E(s_t-E(s_t))(s_t-E(s_t))'$$

i.e 

$$E(\alpha \varepsilon_t^A+ (1-\alpha) \varepsilon_t^B)^2$$

The assumption of independence allows us to get rid of the cross term and simply write 

$$Var(s_t)=\alpha^2 \sigma_A^2+ (1-\alpha)^2\sigma_B^2$$

Since we want a signal with the maximum precision (minimum variance), we choose $\alpha$ to minimize this expression. This turns out to be 

$$\alpha=\frac{\sigma_B^2}{\sigma_A^2+\sigma_B^2}$$

This simply means, that if the measurement error in B has a high variance i.e B is less precise, we put more weight on signal A. That makes sense! We are simply weighting the two signals in the ratio of their relative precision. 

But now, the golden question: Are we doing better than if disregarded the less precise sensor?

It can be seen that 
$$Var(s_t)=\frac{\sigma_A^2\sigma_B^2}{\sigma_A^2+\sigma_B^2} < min(\sigma_A^2,\sigma_B^2)$$

Mission accomplished!

And that's the arcane magic involved in filtering.
