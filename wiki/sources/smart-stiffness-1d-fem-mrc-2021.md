---
title: "Source: Smart stiffness computation of one-dimensional Finite Elements"
type: source
tags: [FEM, stiffness, ANN, FFNN, Sobolev-training, truss, beam, nonlinear]
created: 2026-04-06
updated: 2026-04-06
sources: 1
---
**Introduces three ANN-based methods to replace the tangent stiffness matrix in 1D FEM (truss and beam elements) using Sobolev training, achieving up to 41% simulation speed-up.**

## Key points
- First paper in Tandale's stiffness-replacement series.
- Three methods proposed for stiffness computation: (1) automatic differentiation, (2) stiffness evolution (sequence model), (3) direct stiffness replacement with Sobolev loss.
- Sobolev training: the loss function adds a term for the first derivative (stiffness) alongside the force, so the NN learns both simultaneously.
- A scaling strategy is introduced allowing the same trained NN to predict stiffness for elements with different geometric properties without retraining (valid in geometrically linear regime).
- Training data: 60,000 training / 10,000 validation / 10,000 test samples for truss and beam.
- Hyperparameters: 85 hidden neurons, 8 FC layers (Fig. 1 model); 70 hidden neurons, 9 layers (Fig. 2 model); Adam optimiser; early stopping; 150 epochs.

## Methodology / approach
- Custom FE solver used for data generation and deployment.
- NN deployed at the element level: for each element increment, the NN forward pass replaces both material integration and the Newton–Raphson inner loop.
- Three structural test cases: truss (Structures 1–3) and beam (Structures 4–5) with physically nonlinear (elastoplastic) material.

## Key claims & evidence
- Automatic differentiation method: mixed results — 232% slower for Structure 1 but 16–34% faster for Structures 2–3 (depends on problem complexity).
- Stiffness evolution method: 25–35% faster with MAE ≈ 0.1–0.3%.
- More complex BVPs benefit more from NN stiffness replacement (increasing speed gain with complexity).
- Scaling strategy: works without retraining as long as deformations remain geometrically linear.

## Limitations / caveats
- Only 1D elements (truss, beam); 2D/3D not addressed here (extended in IJNME 2022).
- Speed gain of 41% is modest; more complex problems yield larger gains (demonstrated in IJNME 2022).
- Training cost must be amortised over multiple simulations.

## Quotes worth keeping
> "This new development eliminates the need to perform iterations in the nonlinear solver and leads to a significant acceleration of the Finite Element simulation."

## See also
[[concepts/neural-network-enhanced-fem]], [[concepts/sobolev-training]], [[concepts/stiffness-matrix-replacement]], [[sources/intelligent-stiffness-plate-beam-ijnme-2022]], [[Saurabh Balkrishna Tandale]]
