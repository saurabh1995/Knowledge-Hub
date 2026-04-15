---
title: "Viscoplasticity Modelling"
type: concept
tags: [viscoplasticity, plasticity, constitutive-law, Lemaitre-Chaboche, isotropic-damage, FEM]
created: 2026-04-06
updated: 2026-04-06
---
**Classical and neural network-based approaches to modelling time-dependent, rate-sensitive plastic deformation in solid mechanics.**

## What is viscoplasticity?
Viscoplastic materials exhibit:
- **Plastic deformation** beyond the yield surface.
- **Rate dependence** (strain rate affects stress response).
- **Loading history dependence** (path-dependent behaviour).
Common in metals at elevated temperatures, polymers, biological tissues under cyclic loading.

## Classical model: Lemaitre–Chaboche
The most frequently used constitutive framework in Tandale's papers:
- Combines isotropic and kinematic hardening.
- Rate-dependent yield criterion (overstress formulation).
- Solved via implicit integration (return-mapping algorithm) — iterative, expensive.
- State variables: equivalent plastic strain, backstress tensors, damage variable (optional).

## Plane stress viscoplasticity
In thin plate elements, the out-of-plane stress is constrained to zero (plane stress). The plane stress enforcement requires an additional iterative loop to find the thickness strain increment — an inner iteration that Tandale's MRC 2024 eliminates with an LMU-based self-learning model.

## Isotropic damage
Coupled to viscoplasticity in MRC 2024:
- A scalar damage variable D degrades the elastic modulus.
- Damage evolves according to a classical Lemaitre damage criterion.
- The full coupled viscoplastic + damage system is solved by the self-learning LMU.

## NN-based approaches in Tandale's work
| Paper | NN model | What it replaces |
|-------|---------|-----------------|
| CMAME 2022 | LSTM+FFNN (REIIS) | Implicit material integration (full Lemaitre–Chaboche) |
| CMAME 2024 | SNN (LIF+RLIF) | Plastic corrector step |
| EWCO 2024 | SNN (spiking LMU) | Implicit integration; pseudo-explicit scheme |
| MRC 2024 | LMU+dense | Plane stress enforcement + damage |

## See also
[[concepts/neural-network-enhanced-fem]], [[concepts/physics-informed-neural-networks]], [[concepts/self-learning-nn]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/rnn-plane-stress-damage-mrc-2024]]
