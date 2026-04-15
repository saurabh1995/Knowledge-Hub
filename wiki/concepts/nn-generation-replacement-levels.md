---
title: "NN Generation vs. FEM Replacement Level"
type: concept
tags: [FEM, ANN, SNN, stiffness, constitutive-law, brain-inspired, surrogate, neural-networks]
created: 2026-04-10
updated: 2026-04-10
---
**Second-generation ANNs directly replace the stiffness matrix and internal force vector; brain-inspired SNNs operate one level deeper, replacing the constitutive integration — two distinct replacement strategies with different inputs, outputs, and hardware implications.**

## The key distinction

There are two fundamentally different levels at which a neural network can enter the FEM pipeline:

| Level | What is replaced | NN input → output | NN generation | Phase |
|-------|-----------------|-------------------|---------------|-------|
| **Stiffness matrix** | Full element K and F | strain increment sequence → converged **F** + **K** | 2nd gen (ANN, LSTM) | Phase 1 (2021–2022) |
| **Constitutive law** | Plastic corrector at Gauss points | strain history → stress + material tangent **C** | 3rd gen (SNN, brain-inspired) | Phase 2–3 (2023–2024) |

## Level 1 — Stiffness matrix replacement (ANN/LSTM, not brain-inspired)

The NN is plugged in at the **element level**. For each load increment it directly outputs the converged element internal force vector **F** and tangent stiffness matrix **K**, bypassing:
- Gauss-point integration of the constitutive law
- Assembly of element stiffness from material tangent
- Newton–Raphson inner iterations

Training uses [[concepts/sobolev-training]]: the loss includes both the force error and the derivative (stiffness) error, so **F** and **K** are learned simultaneously from the same network.

Sources: [[sources/smart-stiffness-1d-fem-mrc-2021]] (ANN, 41% speedup), [[sources/intelligent-stiffness-plate-beam-ijnme-2022]] (ANN/RNN, >90% speedup), [[sources/lstm-stiffness-plate-pamm-2022]] (LSTM, 90.6% speedup).

## Level 2 — Constitutive law replacement (SNN, brain-inspired)

The SNN is plugged in at the **Gauss-point level**. For each increment it predicts the **stress and material tangent C** that would result from the viscoplastic return-mapping algorithm. The stiffness matrix **K** = ∫ B^T **C** B dV and internal force vector **F** are then assembled through the standard FEM pipeline — the SNN does not output them directly.

The SNN replaces the plastic corrector step (the Newton iterations inside the constitutive update), not the global Newton–Raphson loop. Residual-based online learning allows self-correction for unseen loading sequences (see [[concepts/self-learning-nn]]).

Sources: [[sources/spiking-nn-viscoplastic-fem-cmame-2024]] (LIF+RLIF, >30% speedup), [[sources/snn-engineering-mechanics-ewco-2024]] (spiking LMU pseudo-explicit, >40% speedup, 1/1000 energy on Xylo-Av2).

## Why the confusion arises

Both levels ultimately produce **F** and **K** that the global solver uses — but the path differs:

```
Level 1 (ANN):  strain history ──→ [ANN] ──→ F, K  (element level, direct)

Level 2 (SNN):  strain history ──→ [SNN] ──→ σ, C
                                              ↓ standard FEM assembly
                                              F, K
```

Because both approaches accelerate the same bottleneck (the expensive per-increment element computation), their speedups are reported side-by-side in the wiki — which can make the two levels appear equivalent when they are not.

## Trade-off summary

| Property | Level 1 (ANN/LSTM) | Level 2 (SNN) |
|----------|-------------------|---------------|
| Direct output of K and F | Yes | No (assembled from C) |
| Bypasses Newton–Raphson | Yes (globally) | Partial (local corrector only) |
| Requires Sobolev training | Yes | No (physics loss on constitutive residual) |
| Generalises to unseen loads | Only after retraining | Yes (online self-learning) |
| Brain-inspired hardware | No (CPU) | Yes (Loihi, Xylo-Av2) |
| Energy reduction | Moderate (CPU speedup) | Up to 1/1000 vs CPU (spiking layers) |
| Speedup (best reported) | >90% | >40% |

The lower raw speedup of SNNs relative to Phase 1 ANNs is offset by energy efficiency, hardware deployability, and self-learning capability.

## See also
[[concepts/neural-network-enhanced-fem]], [[concepts/stiffness-matrix-replacement]], [[concepts/sobolev-training]], [[concepts/self-learning-nn]], [[concepts/spiking-neural-networks]], [[concepts/neuromorphic-computing]], [[sources/intelligent-stiffness-plate-beam-ijnme-2022]], [[sources/snn-engineering-mechanics-ewco-2024]]
