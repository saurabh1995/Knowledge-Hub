---
title: "Physics-Informed Neural Networks"
type: concept
tags: [PINNs, physics, loss-function, deep-learning, PDEs, constitutive-law]
created: 2026-04-06
updated: 2026-04-15
---
**A class of neural networks trained with loss functions that include residuals of governing physical equations (PDEs, ODEs, constitutive relations), enforcing physical consistency alongside data-driven fitting.**

## Standard PINN formulation
Total loss = data loss + physics residual loss:
```
L = L_data + λ · L_physics
```
where L_physics is evaluated from the residual of the governing PDE/ODE at collocation points, requiring little or no labelled data.

## Variants in Tandale's work

### REIIS (CMAME 2022)
- **Recurrent Enhanced Implicit Integration Scheme** — an LSTM + FFNN model guided by the constitutive equations of the Lemaitre–Chaboche viscoplastic model.
- Physical constraints are embedded as additional loss terms corresponding to the residuals of the constitutive ODEs.
- Combines data-driven training with physics compliance, improving robustness beyond a purely data-driven model.

### Self-learning PINNs (vs. standard)
Tandale distinguishes his **self-learning** approach from standard PINNs:
- Standard PINNs: physics residual guides training *before* deployment; frozen at inference.
- Self-learning (CMAME 2024, EWCO 2024, MRC 2024): the physics-based loss continues to drive weight updates *during* FE simulation — the model learns online from the physical response of the problem being solved, without pre-collected data.

## Physics constraints used in Tandale's papers
- Viscoplastic constitutive equations (Lemaitre–Chaboche model)
- Plane stress conditions (MRC 2024)
- Sobolev derivative constraints (MRC 2021, IJNME 2022, PAMM 2022) — related but distinct form of physical constraint

## Relation to [[concepts/self-learning-nn]]
Self-learning NNs extend PINNs by keeping the physics loss active during online deployment rather than freezing it after offline training.

## Mathematical formulation

### REIIS combined loss (CMAME 2022 / CMAME 2024)

The total training loss decomposes as:

$$\mathcal{L} = \lambda_1\, \mathcal{L}_{\text{data}} + \lambda_2\, \mathcal{L}_{\text{physics}}$$

- $\mathcal{L}_{\text{data}} = \frac{1}{N_s}\sum_s \frac{1}{l_s}\sum_t \|\Delta\dot{\bar{\varepsilon}}^p_{\text{actual}} - \Delta\dot{\bar{\varepsilon}}^p_{\text{pred}}\|^2$ — supervised on labelled increments.
- $\mathcal{L}_{\text{physics}} = $ residual of the implicit viscoplastic integration at the predicted strain increment, i.e. the yield-function-based residual $\|\Phi(\Delta\dot{\bar{\varepsilon}}^p_{\text{pred}})\|^2$.
- Offline pretraining: $\lambda_1 = \lambda_2 = 1$.
- Online self-learning: $\lambda_1 = 0$, $\lambda_2 = 1$ (data-free).

### Physics loss for MAML (NPJ 2026, Eq. 8)

$$\mathcal{L}_{\text{physics}}(\Delta\bar{\varepsilon}^p) = \left\|J_2\!\left(\boldsymbol{\sigma}'(\Delta\bar{\varepsilon}^p) - \boldsymbol{X}(\Delta\bar{\varepsilon}^p)\right) - k - K\!\left(\frac{\Delta\bar{\varepsilon}^p}{\Delta t}\right)^{1/n} - \left(R_{t-1} + b_1 b_2\,\Delta\bar{\varepsilon}^p\right)/\left(1 + b_1\,\Delta\bar{\varepsilon}^p\right)\right\|^2$$

This single residual equation encodes the full viscoplastic constitutive law and can guide the NN to convergence without any labelled data.

## See also
[[concepts/self-learning-nn]], [[concepts/neural-network-enhanced-fem]], [[concepts/viscoplasticity-modelling]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/rnn-plane-stress-damage-mrc-2024]]
