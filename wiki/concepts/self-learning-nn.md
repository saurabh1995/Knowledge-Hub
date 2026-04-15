---
title: "Self-Learning Neural Networks"
type: concept
tags: [self-learning, online-learning, physics-informed, FEM, viscoplasticity, SNN, RNN]
created: 2026-04-06
updated: 2026-04-06
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

## See also
[[concepts/physics-informed-neural-networks]], [[concepts/spiking-neural-networks]], [[concepts/meta-learning-maml]], [[concepts/recurrent-neural-networks-in-mechanics]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/rnn-plane-stress-damage-mrc-2024]], [[sources/meta-learning-hybrid-spiking-npj-2026]]
