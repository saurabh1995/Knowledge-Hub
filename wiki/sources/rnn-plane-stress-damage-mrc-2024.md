---
title: "Source: Recurrent neural networks as a physics-based self-learning solver to satisfy plane stress viscoplasticity undergoing isotropic damage"
type: source
tags: [LMU, RNN, self-learning, plane-stress, viscoplasticity, isotropic-damage, FEM, explicit-integration, MRC]
created: 2026-04-06
updated: 2026-04-06
sources: 1
---
**Introduces an LMU-based self-learning RNN deployed in the explicit integration scheme to enforce the plane stress condition and solve viscoplasticity with isotropic damage — eliminating iterative root-finding without labelled deployment data.**

## Key points
- **Legendre Memory Unit (LMU)** + dense transformations used as the surrogate model.
- Deployed in the **explicit integration** scheme (unlike CMAME 2022/2024 which target implicit integration).
- Specific task: predicting the **thickness strain increment** that satisfies the plane stress constraint at each time step — replacing the iterative root-finding (e.g., Brent's method or Newton) needed classically.
- **Isotropic damage** coupled to viscoplasticity — scalar damage variable D degrades elastic modulus per Lemaitre criterion.
- **Self-learning** differentiator: the model self-learns online through the physics-based loss without requiring labelled data — explicitly distinguished from standard PINNs.
- Co-authors: Tandale, Sharma, Polydoras, Stoffel.

## Methodology / approach
1. LMU+dense pretrained offline with combined data-driven and physics-based loss.
2. Deployed in explicit FE integration: at each step, the LMU predicts the thickness strain increment satisfying plane stress.
3. Online physics-based loss drives self-learning if the constraint is not satisfied — no external data needed.
4. Results compared against classical explicit integration (with iterative plane stress enforcement).

## Key claims & evidence
- Elimination/reduction of iterations to satisfy the plane stress condition → faster convergence.
- Self-learning without data distinguishes the approach from PINNs (which still need data or collocation points at training).
- Validated against classical explicit integration for plate BVPs with viscoplasticity + isotropic damage.

## Limitations / caveats
- Explicit integration context — not directly comparable to the implicit integration papers (CMAME 2022, 2024).
- Limited to plate elements.
- Quantitative speed gains not as prominently reported as in the EWCO 2024 paper.

## Quotes worth keeping
> "The proposed self-learning method differentiates itself from the well-known Physics-Informed Neural Networks (PINNs) since the neural network model self-learns online through the proposed physics-based loss term without the need for data."

## See also
[[concepts/self-learning-nn]], [[concepts/physics-informed-neural-networks]], [[concepts/viscoplasticity-modelling]], [[concepts/recurrent-neural-networks-in-mechanics]], [[sources/snn-engineering-mechanics-ewco-2024]], [[Saurabh Balkrishna Tandale]]
