---
title: "Meta-Learning (MAML)"
type: concept
tags: [meta-learning, MAML, few-shot, initialisation, gradient, SNN, FEM]
created: 2026-04-06
updated: 2026-04-15
---
**A learning-to-learn strategy that trains model parameters to be a good initialisation point for rapid adaptation to new tasks, using second-order gradients.**

## What is MAML?
**Model-Agnostic Meta-Learning (MAML)** (Finn et al.) optimises model parameters θ such that a small number of gradient steps on a new task produces large performance gains. The outer loop minimises task loss *after* k inner gradient steps:

$$\theta^* = \arg\min_\theta \sum_{\tau \sim p(\mathcal{T})} \mathcal{L}_\tau\!\left(\theta - \alpha \nabla_\theta \mathcal{L}_\tau(\theta)\right)$$

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

## Mathematical formulation

### MAML inner and outer loops (NPJ 2026)

**Inner loop** (task-specific adaptation, $k=1$ gradient step in NPJ 2026):

$$\theta'_\tau = \theta - \alpha\, \nabla_\theta\, \mathcal{L}_\tau^{\text{train}}(\theta)$$

**Outer loop** (meta-update across tasks):

$$\theta \leftarrow \theta - \beta\, \nabla_\theta \sum_{\tau \in \mathcal{T}_{\text{val}}} \mathcal{L}_\tau^{\text{val}}(\theta'_\tau)$$

- $\alpha$: inner loop learning rate; $\beta$: outer (meta) learning rate.
- $\mathcal{L}$ is the combined physics-based + data-driven loss (see [[concepts/physics-informed-neural-networks]]).
- In NPJ 2026: 100 sequences for inner loop ($D_{\text{tr}}$), 250 for outer loop ($D_{\text{val}}$); max 100 outer epochs.
- Early stopping: $|\nabla_\theta \mathcal{L}| < 10^{-5}$ or test MSE $< 10^{-4}$.

### FE deployment (inner loop only)

During FEM simulation, only the inner loop adaptation runs online:

$$\theta'_{\text{online}} = \theta_{\text{pretrained}} - \alpha\, \nabla_\theta\, \mathcal{L}_{\text{physics}}(\theta_{\text{pretrained}})$$

Outer loop is triggered periodically on pretraining sequences to prevent catastrophic forgetting.

### QAT quantization (NPJ 2026, Eq. 1)

$$\tilde{m} = \left\lfloor \frac{m - m_{\min}}{s_m} \right\rceil, \qquad s_m = \frac{m_{\max} - m_{\min}}{2^n - 1}$$

- $n$: number of bits (32-bit integer on Loihi 2); $s_m$: scaling factor.
- Applied to both weights and activations of the HSN layers post-meta-training.

## See also
[[concepts/self-learning-nn]], [[concepts/spiking-neural-networks]], [[sources/meta-learning-hybrid-spiking-npj-2026]]
