---
title: "Source: Physically constrained deep recurrent neural network for stiffness computation of plate structures"
type: source
tags: [LSTM, stiffness, plate, Sobolev-training, FEM, nonlinear, elastoplasticity, PAMM]
created: 2026-04-06
updated: 2026-04-06
sources: 1
---
**Conference paper extending LSTM-based stiffness replacement to 2D plate elements, with Hyperband hyperparameter tuning, achieving 90.6% simulation speed-up.**

## Key points
- Extends the stiffness replacement framework (MRC 2021, IJNME 2022) to **plate structures** using an LSTM model.
- LSTM is used because it inherently captures path-dependent behaviour through its internal gating mechanism.
- Sobolev training applied: additional loss term for the derivative (stiffness), combined with standard data-driven loss.
- Hyperband search algorithm used for hyperparameter optimisation (eliminates manual tuning of search hyperparameters).
- Final hyperparameters: 2 LSTM layers, 128 units, 1 dense layer.
- PAMM (Proceedings in Applied Mathematics and Mechanics) — conference proceedings.

## Methodology / approach
- LSTM model trained sequentially to learn the evolution of internal force vector and stiffness matrix for plate elements.
- Loss: combined data-driven (force residual) + Sobolev (stiffness residual) summed over all time steps and sequences.
- BVP: plate with two clamped sides, external load applied; 13 integration points per mid-surface element.
- Deployed in FE solver at element level; updates internal force and stiffness without Newton iterations.

## Key claims & evidence
- **90.564%** simulation speed boost on the academic plate example.
- Acceptable displacement error (stable evolution of internal force vector).
- Retraining needed if deformation pattern changes significantly (same caveat as IJNME 2022).

## Limitations / caveats
- Conference paper format (6 pages) — full validation scope is limited.
- Results shown on one academic BVP only.

## See also
[[concepts/stiffness-matrix-replacement]], [[concepts/sobolev-training]], [[concepts/recurrent-neural-networks-in-mechanics]], [[concepts/neural-network-enhanced-fem]], [[sources/intelligent-stiffness-plate-beam-ijnme-2022]], [[Saurabh Balkrishna Tandale]]
