---
title: "Self-Learning Neural Networks"
type: concept
tags: [self-learning, online-learning, physics-informed, FEM, viscoplasticity, SNN, RNN]
created: 2026-04-06
updated: 2026-04-15
---
**Neural networks that continue updating their weights during deployment (online training), guided by physics-based loss functions rather than labelled data.**

## Key distinction from standard approaches

| Approach | Training phase | Deployment phase |
|----------|---------------|-----------------|
| Data-driven NN | Offline on labelled dataset | Frozen weights |
| PINN | Offline with physics loss | Frozen weights |
| **Self-learning NN** | Offline pretraining (hybrid loss) | **Online updates via physics loss** |

## Mechanism
1. **Pretraining**: the model is pretrained offline using a combination of data-driven and physics-based loss functions.
2. **Deployment**: embedded in the FE solver. At each time step, if the physics residual exceeds a tolerance, the model performs online gradient steps — no external labelled data required.
3. **Convergence**: the self-learning phase eliminates or drastically reduces Newton–Raphson or material integration iterations.

## Where applied in Tandale's work
| Paper | Model | Physics constraint used online |
|-------|-------|-------------------------------|
| CMAME 2022 | LSTM+FFNN (REIIS) | Lemaitre–Chaboche viscoplastic ODEs |
| MRC 2024 | LMU+dense | Plane stress + viscoplasticity + isotropic damage |
| CMAME 2024 | SNN (LIF+RLIF) | Viscoplastic constitutive equations |
| EWCO 2024 | SNN (spiking LMU) | Viscoplastic + implicit integration |
| NPJ 2026 | HSNN (HSN neurons) | Viscoplastic; meta-pretrained via MAML |

## Pseudo-explicit behaviour
Once pretrained well (especially with meta-learning, see [[concepts/meta-learning-maml]]), the model requires few or no online iteration steps — behaving as a pseudo-explicit scheme despite being embedded in an implicit solver (EWCO 2024).

## Advantages
- No need for labelled data during deployment.
- Model adapts to the specific loading path being solved.
- Compatible with neuromorphic hardware when SNN-based.

## Limitations
- Requires careful pretraining to avoid divergence during online learning.
- Meta-learning (MAML) improves initialisation robustness (NPJ 2026).

## Mathematical formulation

### Combined pretraining loss (CMAME 2024, Eq. 47–48)

$$\mathcal{L}_{\text{data}} = \frac{1}{N_s}\sum_{s=1}^{N_s} \frac{1}{T_s}\sum_{t=1}^{T_s}\left\|\Delta\dot{\bar{\varepsilon}}^p_{\text{actual},t} - \Delta\dot{\bar{\varepsilon}}^p_{\text{pred},t}\right\|^2$$

$$\mathcal{L}_{\text{physics}} = \frac{1}{N_s}\sum_{s=1}^{N_s} \frac{1}{T_s}\sum_{t=1}^{T_s}\left\|f_{\text{constitutive}}\!\left(\Delta\dot{\bar{\varepsilon}}^p_{\text{pred},t}\right)\right\|^2$$

where $f_{\text{constitutive}}$ is the residual of the implicit viscoplastic integration (yield function evaluated at the predicted strain increment). Total loss:

$$\mathcal{L} = \lambda_1\, \mathcal{L}_{\text{data}} + \lambda_2\, \mathcal{L}_{\text{physics}}$$

| Phase | $\lambda_1$ | $\lambda_2$ |
|-------|------------|------------|
| Offline pretraining | 1 | 1 |
| Online self-learning (deployment) | 0 | 1 |

### Trigger condition and weight update

At each FEM time step, after the NN forward pass:
- If $\mathcal{L}_{\text{physics}} \le 10^{-6}$: accept prediction (no update needed).
- If $\mathcal{L}_{\text{physics}} > 10^{-6}$: trigger online back-propagation with $\mathcal{L}_{\text{physics}}$ only.
- In CMAME 2024: **LIF/RLIF spiking weights are frozen** during online updates; only dense (decoder) layers are updated to prevent loss of spiking history.
- In NPJ 2026: **only the last dense layer** adapts per inner MAML loop step.

## See also
[[concepts/physics-informed-neural-networks]], [[concepts/spiking-neural-networks]], [[concepts/meta-learning-maml]], [[concepts/recurrent-neural-networks-in-mechanics]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/rnn-plane-stress-damage-mrc-2024]], [[sources/meta-learning-hybrid-spiking-npj-2026]]
