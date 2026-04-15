---
title: "Source: Dissertation — Sustainable Brain-Inspired Networks and Deep-Learning Methods in Structural Mechanics"
type: source
tags: [dissertation, FEM, ANN, SNN, neuromorphic, self-learning, viscoplasticity, LIF, LMU, MAML]
created: 2026-04-07
updated: 2026-04-07
sources: 1
---
**PhD thesis by Saurabh Balkrishna Tandale (RWTH Aachen, defended 12 Sept 2024) — the umbrella document unifying his 2021–2026 research programme on neural network-enhanced FEM and neuromorphic computing.**

## Key points
- 240-page cumulative dissertation based on 10 peer-reviewed journal publications
- Examiner: apl. Prof. Marcus Stoffel (supervisor); reviewer: Univ.-Prof. Julia Kowalski
- Institute Report No. IAM-22, RWTH Aachen University, 2024
- Covers the complete arc from second-generation ANNs through to third-generation SNNs deployed on neuromorphic chips

## Structure / methodology
The dissertation is organised into 8 chapters:

| Chapter | Topic |
|---------|-------|
| 1 | Introduction — motivation, state of the art, contributions |
| 2 | Second-generation ANNs: FFNN, RNN, LSTM, GRU, CNN, hyperparameter tuning |
| 3 | From ANNs to SNNs: LIF, RLIF, spiking LMU, surrogate gradient learning, encoding/decoding, brain-inspired regularisation |
| 4 | Structural mechanics: continuum mechanics, shell theory, Lemaitre–Chaboche viscoplasticity, FEM |
| 5 | Data-driven surrogate models: encoder-decoder RNNs, attention mechanism, spiking LMU for shock-wave plates |
| 6 | Numerically constrained NNs (stiffness replacement, Sobolev training) |
| 7 | Physics-based self-learning FEM: implicit integration with LSTM + SNN; Xylo-Av2 deployment |
| 8 | Conclusions and outlook |

## Key claims & evidence
- A unified framework is established for integrating second- and third-generation NNs into FEM at the element (stiffness) and Gauss-point (constitutive) levels
- Spiking LMU + autoencoding enables SNNs to handle real-valued engineering data (spike trains are not naturally real-valued)
- Physics-based self-learning eliminates need for labelled training data; the constitutive residual serves as unsupervised loss
- Speed gains: 41% (1D, MRC 2021) → 90.6% (plates, PAMM 2022) → >30–40% (SNNs, CMAME/EWCO 2024)
- Energy: SNN on Xylo-Av2 achieves 1/1000 of GPU-equivalent energy (EWCO 2024)
- Hybrid Spiking Neurons (HSN) + MAML meta-learning (NPJ 2026) improve pretraining convergence for self-learning FEM

## Limitations / caveats
- All FEM benchmarks are on 1D/2D (truss, beam, plate) structural elements; 3D solid elements not demonstrated
- Material model is exclusively Lemaitre–Chaboche viscoplasticity; no hyperelastic or multi-physics cases
- Training at element level: element type or mesh changes may require retraining

## Contradictions with existing wiki
- None; this is the primary consolidation document. Confirms all major claims already captured in individual source pages.

## Quotes worth keeping
> "This thesis aims to provide a framework for using third-generation networks for nonlinear function approximation that can be deployed on a neuromorphic chip, integrating second and third-generation network models into FEM, accelerating computational speed, and developing self-learning neural networks for solving nonlinear systems of equations."

## Publication list (from dissertation)
- MRC 2022 (smart stiffness 1D), IJNME 2022 (plate/beam stiffness), CMAME 2022 (REIIS self-learning), PAMM 2022 (LSTM plate), ABME 2023 (lumbar spine), CM 2023 (attention encoder-decoder), CMAME 2023 (spiking RNN), CMAME 2024 (SNN self-learning viscoplastic), EWCO 2024 (brain-inspired SNN FEM), NPJ 2024 (SNN regression), NPJ 2026 (HSN+MAML)
- Patent (DE): System for displaying internal stress states of mechanical components (2023)

## See also
[[Saurabh Balkrishna Tandale]], [[concepts/neural-network-enhanced-fem]], [[concepts/self-learning-nn]], [[concepts/spiking-neural-networks]], [[concepts/neuromorphic-computing]], [[concepts/sobolev-training]], [[concepts/meta-learning-maml]]
