---
title: 'The Unscented Kalman Filter'
date: 2023-10-14
permalink: /posts/2023/10/unscented-kalman-filter/
excerpt: "An introduction and overview of the Unscented Kalman Filter"
tags:
  - state space methods
  - kalman filter
  - bayesian inference
---

# Understanding the Unscented Kalman Filter

Typically, we apply the Kalman Filter to solve inference problems in linear systems. But the Kalman Filter algorithm can be applied for inference in non-linear systems as well under the assumption of Gaussianity. The catch? A Gaussian remains a Gaussian under a linear transformation but not under a non-linear one. The Extended Kalman Filter does this through the use of Taylor's approximation to linearize the measurement and dynamics equations. However, highly non-linear systems may have unreasonably low tolerance for mis-specification errors. This is where the Unscented Kalman Filter comes handy, where we approximate the latent-state distribution not just using the mean, but a set of carefully chosen points around the mean. 

## Introduction

The Unscented Kalman Filter (UKF) is an extension of the classic Kalman Filter (KF) used for nonlinear systems. While the KF is suitable for linear systems, the UKF addresses the limitations of the Extended Kalman Filter (EKF) by using a deterministic sampling approach to capture the mean and covariance estimates accurately.

## The Unscented Transformation

The core of the UKF is the Unscented Transformation (UT). It involves generating a set of points (sigma points) around the mean. These points are then propagated through the nonlinear functions, from which the new mean and covariance estimates are derived.

```python
import numpy as np

def generate_sigma_points(mean, covariance, kappa):
    n = len(mean)
    sigma_points = np.zeros((2 * n + 1, n))
    sigma_points[0] = mean
    W = np.linalg.cholesky((n + kappa) * covariance)
    
    for i in range(n):
        sigma_points[i + 1] = mean + W[:, i]
        sigma_points[n + i + 1] = mean - W[:, i]
    
    return sigma_points
```

## Prediction and Update Steps

The UKF consists of two main steps: prediction and update. The prediction step propagates the sigma points through the process model, and the update step adjusts the estimates based on the new measurements.

### Prediction Step

```python
def predict_sigma_points(sigma_points, process_function):
    predicted_sigma_points = []
    for point in sigma_points:
        predicted_sigma_points.append(process_function(point))
    return np.array(predicted_sigma_points)
```

### Update Step

```python
import numpy as np

def ukf_update(predicted_sigma_points, measurement_function, measurement, R):
    n_sigma_points = predicted_sigma_points.shape[0]
    n_state_dimensions = predicted_sigma_points.shape[1]
    n_measurement_dimensions = measurement.shape[0]

    # Measurement prediction
    predicted_measurements = np.array([measurement_function(point) for point in predicted_sigma_points])
    mean_measurement = np.mean(predicted_measurements, axis=0)

    # Measurement covariance
    S = np.zeros((n_measurement_dimensions, n_measurement_dimensions))
    for z in predicted_measurements:
        S += np.outer(z - mean_measurement, z - mean_measurement)
    S /= n_sigma_points
    S += R  # Add measurement noise covariance

    # Cross-covariance matrix between state and measurements
    C = np.zeros((n_state_dimensions, n_measurement_dimensions))
    for i in range(n_sigma_points):
        C += np.outer(predicted_sigma_points[i] - predicted_sigma_points.mean(axis=0),
                      predicted_measurements[i] - mean_measurement)
    C /= n_sigma_points

    # Kalman gain
    K = C @ np.linalg.inv(S)

    # Update state estimate and covariance
    mean_state_update = K @ (measurement - mean_measurement)
    updated_mean = predicted_sigma_points.mean(axis=0) + mean_state_update
    updated_covariance = C - K @ S @ K.T

    return updated_mean, updated_covariance

```

## Conclusion

The UKF provides a more accurate and robust approach for state estimation in nonlinear systems compared to the EKF. Its ability to handle highly nonlinear dynamics makes it a valuable tool in various fields, from robotics to financial engineering.

---

This blog post provides a basic introduction and some code snippets for implementing the Unscented Kalman Filter. Depending on the audience, you might want to expand on each section, provide more detailed examples, or include more complex mathematical explanations.

------