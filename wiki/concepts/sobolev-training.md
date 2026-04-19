---
title: "Sobolev Training"
type: concept
tags: [training, neural-networks, loss-function, derivative, FEM, stiffness]
created: 2026-04-06
updated: 2026-04-19
---
**A neural network training procedure that adds a derivative-matching loss term alongside the standard function-fitting loss, ensuring the NN learns both a quantity and its gradient simultaneously.**

## Motivation
In [[concepts/neural-network-enhanced-fem]], the stiffness matrix is the first derivative of the internal force vector with respect to displacement. A standard data-driven NN trained only on force values will not reliably predict the stiffness (its gradient). Sobolev training addresses this directly.

## Effect on training
- The NN approximates the function *and* its first derivative simultaneously.
- Leads to improved accuracy in stiffness prediction.
- Enables the trained network to be deployed without further Newton iterations in the FE solver.
- Relates to Sobolev spaces (W^{1,p}) in functional analysis — hence the name.

## Where used in Tandale's work
| Paper | NN type | What derivative is learned |
|-------|---------|--------------------------|
| MRC 2021 | FFNN | Stiffness from internal force (1D elements) |
| IJNME 2022 | FFNN/RNN | Stiffness for truss/beam/plate |
| PAMM 2022 | LSTM | Stiffness for plate elements |

## Mathematical formulation

Standard Sobolev loss — force-fitting augmented with stiffness-derivative matching:

$$\mathcal{L} = \mathcal{L}_{\text{data}} + \lambda\,\mathcal{L}_{\text{deriv}}$$

$$\mathcal{L}_{\text{data}} = \|\mathbf{F}_{\text{pred}} - \mathbf{F}_{\text{act}}\|^2, \quad \mathcal{L}_{\text{deriv}} = \|\mathbf{K}_{\text{pred}} - \mathbf{K}_{\text{act}}\|^2$$

where **F** is the internal force vector, **K** is the tangent stiffness matrix, and λ weights the derivative term.

For the RNN/sequence formulation (PAMM 2022), summed over load increments p and elements i ([[sources/lstm-stiffness-plate-pamm-2022]] — Eq. 6):

$$\mathcal{L} = \frac{1}{T}\sum_{p=1}^{T} \frac{1}{l_p}\sum_{i}\left[\|\mathbf{F}_{a,i} - \mathbf{F}_{p,i}\|^2 + \|\mathbf{K}_{a,i} - \mathbf{K}_{p,i}\|^2\right]$$

where T is the number of load increments, $l_p$ is the number of elements at increment p; subscripts a and p denote actual and predicted values.

## See also
[[concepts/neural-network-enhanced-fem]], [[concepts/stiffness-matrix-replacement]], [[sources/smart-stiffness-1d-fem-mrc-2021]], [[sources/intelligent-stiffness-plate-beam-ijnme-2022]], [[sources/lstm-stiffness-plate-pamm-2022]]
