---
title: "Transfer Learning"
type: concept
tags: [transfer-learning, fine-tuning, domain-adaptation, meta-learning, machine-learning]
created: 2026-04-19
updated: 2026-04-19
---
**A machine learning strategy where a model pre-trained on one task is adapted to a new task by fine-tuning some or all weights — contrasted with MAML meta-learning in this wiki because transfer learning requires more data and often fails with small datasets or frozen layers.**

## Core idea

Transfer learning assumes that representations learned on a source domain are useful for a target domain. The standard recipe:

1. Pre-train a model on a large source dataset (or a related task).
2. Freeze lower layers (feature extractor) and fine-tune only the final layers on the target task.
3. Optionally unfreeze more layers as more target data becomes available.

The intuition: early layers learn general features; only the last layers need to adapt.

## Where it works and where it fails

### Success cases
- Large pre-trained backbone + small adaptation target (e.g., ImageNet → medical image classification)
- When the source and target distributions are closely aligned
- When the network can afford to freeze most weights (shallow adaptation)

### Failure cases in this wiki

**Case 1 — Small biomedical datasets (CMPB 2021)**
Transfer learning from pre-trained ImageNet CNNs was attempted for classifying BMSC/tenocyte/chondrocyte morphologies from phase-contrast images. The domain gap (natural images → microscopy) and extremely small training set caused transfer learning to underperform models trained from scratch with domain-specific augmentation. ([[sources/cnn-tenogenic-differentiation-cmpb-2021]] — Limitations section)

**Case 2 — FEM adaptation across loading sequences**
When adapting a pre-trained SNN to a new boundary value problem (BVP) with a different loading sequence, transfer learning (freezing spiking layers, adapting only dense layer) requires approximately **30 training sequences** to match meta-learned performance. Frozen layers create convergence problems because the spiking layers cannot adapt to the new loading regime. ([[concepts/solver-strategies-at-gaussian-points]] — Comparison: self-learning vs. meta-learning; [[sources/meta-learning-hybrid-spiking-npj-2026]], page 5)

## Transfer learning vs. MAML in FEM context

| Aspect | Transfer learning | MAML (meta-learning) |
|--------|------------------|----------------------|
| Adaptation mechanism | Freeze lower layers, fine-tune upper | Full network adapted via inner loop |
| Training sequences needed (new BVP) | ~30 sequences | 5 sequences |
| Frozen layer risk | Convergence problems in spiking layers | No frozen layers; outer loop prevents forgetting |
| Pre-training objective | Task performance on source domain | Optimal initialisation for fast adaptation |
| Data dependency | Requires labelled target data | Physics-based loss only (no labels needed) |

## Transfer learning vs. self-learning in FEM context

[[concepts/self-learning-nn]] is distinct from both:
- Self-learning updates weights **online during deployment** using physics loss — no pre-training transfer assumed
- Transfer learning adapts **offline** before deployment
- MAML optimises the **pre-training initialisation** so self-learning converges faster

## Mathematical formulation

Standard fine-tuning update (gradient descent on target loss $\mathcal{L}_T$ with pre-trained parameters $\theta_0$):

$$\theta^* = \theta_0 - \alpha \nabla_{\theta} \mathcal{L}_T(\theta_0)$$

The problem: if layers are frozen, $\nabla_{\theta}$ is blocked from propagating to earlier weights. For FEM self-learning (physics loss), the frozen spiking layers cannot correct their constitutive predictions, leading to convergence failure.

MAML replaces the fixed $\theta_0$ with a meta-optimised initialisation (see [[concepts/meta-learning-maml]] for full equations).

## See also
[[concepts/meta-learning-maml]], [[concepts/self-learning-nn]], [[sources/meta-learning-hybrid-spiking-npj-2026]], [[sources/cnn-tenogenic-differentiation-cmpb-2021]], [[concepts/neural-network-enhanced-fem]]
