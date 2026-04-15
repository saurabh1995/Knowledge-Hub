---
title: "Stiffness Matrix Replacement"
type: concept
tags: [FEM, stiffness, surrogate, neural-networks, nonlinear, plasticity]
created: 2026-04-06
updated: 2026-04-06
---
**An approach within NN-enhanced FEM where the element tangent stiffness matrix — not just the constitutive law — is predicted directly by a neural network, eliminating Newton iterations.**

## What is replaced
In classical nonlinear FEM:
1. Material integration at Gauss points → stress + material tangent **C**
2. Assembly of element stiffness: **K** = ∫ B^T **C** B dV
3. Newton–Raphson solver iterates until residual < tolerance

Stiffness replacement bypasses steps 1–3 at the element level: the NN directly outputs the converged internal force vector and stiffness matrix for a given strain/displacement sequence.

## Three methods (MRC 2021 / IJNME 2022)

| Method | Input | Output | Notes |
|--------|-------|--------|-------|
| Automatic differentiation | strain history | internal force | stiffness via AD backward pass |
| Stiffness evolution | strain increment sequence | converged K | sequence model learns K trajectory |
| Direct stiffness replacement | strain at int. point | F + K | Sobolev loss for both outputs |

## Scaling strategy
Because the NN is trained at the element level on a specific geometry, a scaling strategy was introduced (MRC 2021) so that elements with *different* geometric properties can reuse the same trained network without retraining — valid in the geometrically linear regime.

## Key results
- Speed gains >90% for complex BVPs (IJNME 2022).
- Quadratic convergence of the global solver is preserved.
- Break-even: training cost paid off after ~10 repeated simulations.

## Limitations
- Currently demonstrated on 1D (truss, beam) and 2D (plate) elements.
- Scaling strategy limited to geometrically linear problems.
- Network must be retrained for significantly different deformation patterns.

## See also
[[concepts/neural-network-enhanced-fem]], [[concepts/sobolev-training]], [[sources/smart-stiffness-1d-fem-mrc-2021]], [[sources/intelligent-stiffness-plate-beam-ijnme-2022]], [[sources/lstm-stiffness-plate-pamm-2022]]
