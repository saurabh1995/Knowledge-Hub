---
title: "Physics-Informed Neural Networks"
type: concept
tags: [PINNs, physics, loss-function, deep-learning, PDEs, constitutive-law]
created: 2026-04-06
updated: 2026-04-06
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

## See also
[[concepts/self-learning-nn]], [[concepts/neural-network-enhanced-fem]], [[concepts/viscoplasticity-modelling]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/rnn-plane-stress-damage-mrc-2024]]
