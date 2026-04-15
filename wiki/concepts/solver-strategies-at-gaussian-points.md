---
title: "Solver strategies at Gaussian points"
type: concept
tags: [FEM, Gaussian-points, solvers, self-learning, meta-learning, implicit-integration, viscoplasticity]
created: 2026-04-13
updated: 2026-04-13
---

**Evolution of numerical methods for solving nonlinear systems of equations at Gaussian points in FEM, from classical iterative solvers to self-learning and meta-learning approaches.**

## Classical root-finding solvers

Traditional approaches for solving implicit constitutive integrations at Gaussian points:

| Solver | Type | Requirements | Context |
|--------|------|--------------|---------|
| **Newton-Raphson** | Gradient-based iterative | Derivative information | Standard FEM; requires multiple iterations per load increment ([[sources/meta-learning-hybrid-spiking-npj-2026]]) |
| **Pegasus method** | Root-finding | Two initial guesses | Alternative to Newton-Raphson for viscoplastic equations; reduces derivative cost ([[sources/meta-learning-hybrid-spiking-npj-2026]]) |
| **False position method** | Bracketing/root-finding | Two initial guesses | Conservative root-finding approach ([[sources/meta-learning-hybrid-spiking-npj-2026]]) |
| **Backward Euler** | Implicit time integration | Residual from implicit integration | Used to set up nonlinear system; generates physics-based loss for NN training ([[sources/meta-learning-hybrid-spiking-npj-2026]]) |

All require convergence iterations at each Gaussian point per load increment.

## Self-learning neural network solvers

Physics-based approach replacing iterative methods:

**Method:** NN pre-trained on hybrid physics-data loss, deployed in implicit integration scheme to predict converged solution with minimal/no online iterations.

**Advantages:**
- Data-independent after deployment (uses only physics-based loss)
- Eliminates iterative inner loops at Gaussian points
- Maintains accuracy vs. classical solvers

**Limitation:** Requires online training for deformation modes not seen during pretraining.

## Meta-learning solvers (MAML-based)

Second-order gradient approach for robust adaptation:

**Strategy:** MAML pretrains network initialization θ₀ such that k inner loop gradient steps on any new loading sequence minimize physics residual. Deployed in FEM with self-learning inner loop.

**Key parameters:**
- **Inner loop:** Physics-based loss only (Lₚ); adapts to unseen sequences (Eq. 18 in [[sources/meta-learning-hybrid-spiking-npj-2026]])
- **Outer loop:** Meta-gradient on validation data; prevents catastrophic forgetting
- **Surrogate gradient descent:** Enables differentiation through spiking activation (arctan surrogate)

**Advantages:**
- Rapid adaptation without data dependency
- Feature reuse vs. rapid learning: model develops "skill" to solve different tasks (Fig. 4 in [[sources/meta-learning-hybrid-spiking-npj-2026]])
- Superior to transfer learning: 5 training sequences + 38 MAML epochs vs. ~30 sequences for transfer learning (Fig. 5 in [[sources/meta-learning-hybrid-spiking-npj-2026]])

## Bounded softplus activation for guaranteed convergence

Final-layer activation function ensuring solution bounds:

**Motivation:** Equivalent viscoplastic strain Δεₚ is always positive and must lie within interval containing root. Combines advantages of root-finding (interval guarantee) and gradient-based approaches.

**Role:** Ensures HSNN output always in physically admissible range, enabling faster convergence than unbounded activations ([[sources/meta-learning-hybrid-spiking-npj-2026]]).

## Iteration reduction at Gaussian points

Performance across BVPs:

| BVP | Gaussian points (inelastic) | Classical solver (Pegasus) iterations | NN-based solver iterations | Reduction |
|-----|-----|-----|-----|-----|
| Fig. 6 (BVP1) | 72 | 7,845 | 5,056 | 35% fewer |
| Fig. 7 (BVP2) | 86 | 16,593 | 13,258 | 20% fewer |

(Table 3 in [[sources/meta-learning-hybrid-spiking-npj-2026]])

**Note:** Iteration reduction does not directly translate to proportional wall-clock speedup because single NN forward pass is more expensive than single classical iteration. Significant speedup observed (19% for BVP1, 7.3% for BVP2) when combined with multiple Gaussian points across full simulations.

## Comparison: self-learning vs. meta-learning

Transfer learning (self-learning without MAML) vs. MAML-based meta-learning:

**Transfer learning approach:** Pre-train on one BVP, freeze spiking layers, adapt only dense layer to new BVPs.
- **Result:** ~30 training sequences required for new BVP to match meta-learned performance
- **Limitation:** Frozen layers lead to convergence problems

**Meta-learning (MAML) approach:** Pre-train across multiple BVPs with nested optimization.
- **Result:** 5 training sequences sufficient for new BVP
- **Advantage:** Allows full network adaptation through inner loop; outer loop prevents forgetting

([[sources/meta-learning-hybrid-spiking-npj-2026]], page 5)

## See also

[[concepts/meta-learning-maml]], [[concepts/neural-network-enhanced-fem]], [[concepts/self-learning-nn]], [[concepts/viscoplasticity-modelling]], [[concepts/spiking-neural-networks]], [[sources/meta-learning-hybrid-spiking-npj-2026]]
