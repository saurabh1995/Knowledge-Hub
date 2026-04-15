---
title: "Sobolev Training"
type: concept
tags: [training, neural-networks, loss-function, derivative, FEM, stiffness]
created: 2026-04-06
updated: 2026-04-06
---
**A neural network training procedure that adds a derivative-matching loss term alongside the standard function-fitting loss, ensuring the NN learns both a quantity and its gradient simultaneously.**

## Motivation
In [[concepts/neural-network-enhanced-fem]], the stiffness matrix is the first derivative of the internal force vector with respect to displacement. A standard data-driven NN trained only on force values will not reliably predict the stiffness (its gradient). Sobolev training addresses this directly.

## Loss formulation
The standard loss is augmented with a second term:

```
L = L_data + λ · L_derivative

L_data  = || F_predicted - F_actual ||²
L_deriv = || K_predicted - K_actual ||²
```

where F is the internal force vector and K is the tangent stiffness matrix.

For RNN-based formulations (PAMM 2022), the loss is summed over all time steps in a sequence:

```
L = (1/T) Σ_p (1/l_p) Σ_i [ ||F_a - F_p||² + ||K_a - K_p||² ]
```

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

## See also
[[concepts/neural-network-enhanced-fem]], [[concepts/stiffness-matrix-replacement]], [[sources/smart-stiffness-1d-fem-mrc-2021]], [[sources/intelligent-stiffness-plate-beam-ijnme-2022]], [[sources/lstm-stiffness-plate-pamm-2022]]
