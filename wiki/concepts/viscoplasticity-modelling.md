---
title: "Viscoplasticity Modelling"
type: concept
tags: [viscoplasticity, plasticity, constitutive-law, Lemaitre-Chaboche, isotropic-damage, FEM]
created: 2026-04-06
updated: 2026-04-15
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

## Mathematical formulation

### Lemaitre–Chaboche viscoplastic constitutive equations (CMAME 2022, Eq.; CMAME 2024, Eq. 14–17)

**Plastic flow rule** (associated, kinematic hardening direction):

$$\dot{\boldsymbol{\varepsilon}}^p = \frac{3}{2}\, \dot{\bar{\varepsilon}}^p \frac{\boldsymbol{\sigma}' - \boldsymbol{X}}{J_2(\boldsymbol{\sigma}' - \boldsymbol{X})}$$

**Equivalent plastic strain rate** (overstress / rate-dependent):

$$\dot{\bar{\varepsilon}}^p = \left\langle \frac{J_2(\boldsymbol{\sigma}' - \boldsymbol{X}) - k}{K} \right\rangle^n$$

**Backstress evolution** (kinematic hardening):

$$\dot{\boldsymbol{X}} = \frac{2}{3}\, a\, \dot{\boldsymbol{\varepsilon}}^p - s\, \boldsymbol{X}\, \dot{\bar{\varepsilon}}^p$$

**Isotropic hardening** (included from NPJ 2026 onward):

$$\dot{R} = b_1(b_2 - R)\, \dot{\bar{\varepsilon}}^p$$

**Variables:** $\boldsymbol{\sigma}'$ = deviatoric stress, $\boldsymbol{X}$ = backstress, $\bar{\varepsilon}^p$ = equivalent plastic strain, $R$ = isotropic hardening variable, $J_2$ = Huber–Mises–Hencky stress invariant.

**Material parameters:** $E$, $\nu$, $n$, $a$, $s$, $K$, $k$ (and $b_1, b_2$ for isotropic hardening). See [[concepts/lemaitre-chaboche-parameters]] for paper-by-paper values.

### Implicit integration (backward Euler)

The differential equations above are integrated implicitly at each FE time step via a return-mapping algorithm. The resulting nonlinear system is solved classically by Newton–Raphson / Pegasus iterations — or, in Tandale's work, replaced by the neural network solver. See [[sources/physics-based-rnn-viscoplastic-cmame-2022]] for the full integration algorithm (Algorithm 1 / 2).

## See also
[[concepts/neural-network-enhanced-fem]], [[concepts/physics-informed-neural-networks]], [[concepts/self-learning-nn]], [[concepts/lemaitre-chaboche-parameters]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/rnn-plane-stress-damage-mrc-2024]]
