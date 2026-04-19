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

## Mathematical formulation

### Local residual system at a Gauss point

At each Gauss point during implicit FEM integration, the constitutive update solves for the equivalent viscoplastic strain increment $\Delta\varepsilon_p$ satisfying:

$$r(\Delta\varepsilon_p) = \Delta\varepsilon_p - \Delta t \, \dot{\varepsilon}_p(\boldsymbol{\sigma}(\Delta\varepsilon_p)) = 0$$

where $\dot{\varepsilon}_p$ is the viscoplastic strain rate from the Lemaitre–Chaboche flow rule and $\boldsymbol{\sigma}$ is the updated stress tensor (function of $\Delta\varepsilon_p$ via Backward Euler). All classical solvers below operate on this scalar residual equation.

### Newton-Raphson (local form)

$$\Delta\varepsilon_p^{(k+1)} = \Delta\varepsilon_p^{(k)} - \frac{r\!\left(\Delta\varepsilon_p^{(k)}\right)}{\partial r / \partial \Delta\varepsilon_p \big|^{(k)}}$$

Requires the tangent $\partial r / \partial \Delta\varepsilon_p$ at each iteration. Fast quadratic convergence near the root, but can diverge outside the basin of attraction. ([[sources/meta-learning-hybrid-spiking-npj-2026]])

### Backward Euler integration

The stress update (return-mapping) uses the Backward Euler scheme:

$$\boldsymbol{\sigma}^{n+1} = \boldsymbol{\sigma}^{n} + \mathbf{C} : \left(\Delta\boldsymbol{\varepsilon} - \Delta\varepsilon_p \, \mathbf{N}^{n+1}\right)$$

where $\mathbf{C}$ is the elastic stiffness tensor and $\mathbf{N}^{n+1}$ is the flow direction at the updated stress state. This generates the nonlinear system $r(\Delta\varepsilon_p) = 0$ solved by the methods above. ([[sources/physics-based-rnn-viscoplastic-cmame-2022]] — Section 2)

### Pegasus method (bracketing update rule)

Given two initial guesses $a_0, b_0$ bracketing the root ($r(a_0) \cdot r(b_0) < 0$), the Pegasus update at step $k$:

$$c_k = b_k - r(b_k) \cdot \frac{b_k - a_k}{r(b_k) - r(a_k)}$$

If $r(c_k) \cdot r(b_k) < 0$: set $a_{k+1} = b_k$, $b_{k+1} = c_k$  
If $r(c_k) \cdot r(a_k) < 0$: set $a_{k+1} = c_k$, $b_{k+1} = b_k$, and **rescale** $r(a_{k+1}) \leftarrow r(a_{k+1}) / 2$ (the Pegasus correction that prevents slow convergence).

The rescaling step distinguishes Pegasus from plain false position and gives superlinear convergence. ([[sources/meta-learning-hybrid-spiking-npj-2026]])

### NN-based solver (MAML inner loop)

The MAML-pretrained HSNN replaces one or more Pegasus iterations by predicting $\Delta\varepsilon_p$ directly:

$$\Delta\hat{\varepsilon}_p = f_{\theta}\!\left(\boldsymbol{\sigma}^{\text{trial}}, \varepsilon_p^n, T, \Delta t\right)$$

Output bounded via [[concepts/bounded-softplus-activation]] to interval $[a, b]$, ensuring $r(a) \cdot r(b) < 0$ remains valid. Inner-loop adaptation:

$$\theta' = \theta - \alpha \nabla_{\theta} \mathcal{L}_p(\theta), \quad \mathcal{L}_p = \|r(\Delta\hat{\varepsilon}_p)\|^2$$

(Eq. 18 in [[sources/meta-learning-hybrid-spiking-npj-2026]])

## See also

[[concepts/meta-learning-maml]], [[concepts/neural-network-enhanced-fem]], [[concepts/self-learning-nn]], [[concepts/viscoplasticity-modelling]], [[concepts/spiking-neural-networks]], [[concepts/physics-informed-neural-networks]], [[concepts/bounded-softplus-activation]], [[sources/meta-learning-hybrid-spiking-npj-2026]]
