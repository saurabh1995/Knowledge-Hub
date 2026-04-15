---
title: "Meta-Learning (MAML)"
type: concept
tags: [meta-learning, MAML, few-shot, initialisation, gradient, SNN, FEM]
created: 2026-04-06
updated: 2026-04-06
---
**A learning-to-learn strategy that trains model parameters to be a good initialisation point for rapid adaptation to new tasks, using second-order gradients.**

## What is MAML?
**Model-Agnostic Meta-Learning (MAML)** (Finn et al.) optimises model parameters θ such that a small number of gradient steps on a new task produces large performance gains. The outer loop minimises task loss *after* k inner gradient steps:

```
θ* = argmin_θ Σ_task L(θ - α ∇L(θ))
```

This requires computing gradients of gradients (second-order / Hessian-vector products).

## Why MAML for physics-based self-learning?
In [[concepts/self-learning-nn]], the model must quickly converge during online deployment on unseen loading sequences. Poor initialisation leads to many online gradient steps (slow) or divergence. MAML pre-conditions the weights so that:
- Fewer or zero online iterations are needed to reach the converged FEM solution.
- The model generalises across different loading histories and material parameters.

## Application in NPJ 2026
Tandale & Stoffel apply MAML to **Hybrid Spiking Neural Networks (HSNNs)**:
- Tasks = different loading sequences in viscoplastic FE simulations.
- Meta-pretraining with combined physics-based + data-driven loss.
- Result: MAML-pretrained HSNNs outperform first-order pretrained HSNNs in convergence speed and accuracy.
- Quantization-Aware Training (QAT) applied post-meta-training for Loihi 2 deployment.

## Relation to other concepts
- MAML improves the initialisation of [[concepts/self-learning-nn]] models.
- Applied specifically to [[concepts/spiking-neural-networks]] (HSN variant) in the 2026 paper.
- Enables the pseudo-explicit behaviour described in [[concepts/self-learning-nn]].

## See also
[[concepts/self-learning-nn]], [[concepts/spiking-neural-networks]], [[sources/meta-learning-hybrid-spiking-npj-2026]]
