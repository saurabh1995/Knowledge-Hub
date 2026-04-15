---
title: "Source: Spiking recurrent neural networks for neuromorphic computing in nonlinear structural mechanics"
type: source
tags: [SNN, neuromorphic, LMU, Loihi, spike, autoencoding, shock-wave, plates, CMAME, sustainable-AI]
created: 2026-04-06
updated: 2026-04-15
sources: 1
---
**First introduction of Spiking Neural Networks (third-generation) into solid mechanics BVPs, proposing a hybrid spiking LMU + autoencoding model for shock wave-loaded plates, deployed on Intel Loihi with a 21× energy reduction vs GPU.**

## Key points
- Journal: *Computer Methods in Applied Mechanics and Engineering* 412 (2023) 116095
- Authors: Tandale, Stoffel — RWTH Aachen University
- Marks the pivot from second-generation (RNN/LSTM) to **third-generation (SNN)** neural networks in Tandale's research.
- First SNN surrogate for solid mechanics BVPs (to the best of the authors' knowledge).
- Hybrid model: **Spiking LMU (SLMU)** + **SRNN** (spiking recurrent) + dense layers.
- **Autoencoding strategy**: dense encoder converts real-valued FEM data → binary spikes; SRNN decodes spikes back to real values.
- Materials: copper, steel, aluminum shock wave-loaded plates (experimental data, small + large shock tube).
- Deployed on **Intel Loihi** neuromorphic chip; energy measured and compared with GPU and CPU.
- Training: surrogate gradient (arcus tangent) for non-differentiable spike activation.

## NN architecture (Table 2 — tuned hyperparameters)

| Component | Tuned value | Search range |
|-----------|------------|-------------|
| Hidden SLMU layers | 3 | [1–6] |
| Units per SLMU layer | 64 | [8–256] |
| Memory units per SLMU layer | 100 | [5–250] |
| SRNN units | 128 | [8–256] |
| Hidden dense layers | 4 | [1–6] |
| Units per dense layer | 128 | [8–256] |
| Dropout (%) | 15 | [5–40] |

Hyperband search used. Optimizer: Adam. Training data: windowed sequences of 500 time steps from experiments spanning 15 000 time steps.

## Accuracy results (Table 1 — RMSE displacement)

| Experiment | SLMU (spiking) | LMU (2nd gen) |
|-----------|---------------|--------------|
| Steel training (Fig. 6) | 0.02236 | 0.0188 |
| Copper training (Fig. 7) | 0.0282 | 0.0293 |
| Steel validation (Fig. 8) | 0.0821 | 0.07856 |
| Steel training (Fig. 9) | 0.08675 | 0.09021 |
| Steel validation (Fig. 10) | 0.09698 | 0.10235 |
| Aluminium training (Fig. 11) | 0.0141 | 0.01732 |
| Aluminium validation (Fig. 11) | 0.2422 | 0.2368 |
| Steel repeated loads — training (Fig. 12) | 0.0206 | 0.0152 |
| Steel repeated loads — validation (Fig. 12) | 0.0929 | 0.0839 |

Accuracy of SLMU and LMU is comparable — SNN does not sacrifice accuracy for energy efficiency.

## Energy results (Tables 3–4 — forward pass, nJ)

| Layer | GPU | CPU | Loihi | Loihi+GPU | Loihi+CPU |
|-------|-----|-----|-------|-----------|-----------|
| SLMU layer 1 | 3 434 | 99 081 | 5.41 | 5.41 | 5.41 |
| SLMU layer 2 | 6 331 | 181 641 | 5.38 | 5.38 | 5.38 |
| SLMU layer 3 | 6 331 | 181 641 | 5.39 | 5.39 | 5.39 |
| SRNN layer | 3 100 | 90 000 | 0.075 | 0.075 | 0.075 |
| Dense layer 1 | 610 | 18 000 | 0.027 | 610 | 18 000 |
| Dense layer 2 | 310 | 8 800 | 0.011 | 310 | 8 800 |
| Dense layer 3 | 9.6 | 280 | 0.00032 | 9.6 | 280 |
| **Total** | **20 125.6** | **579 443** | **16.285** | **945.9** | **27 096** |

**Reduction factor (total model): 21.3× vs GPU (Loihi+GPU hybrid); 1664× vs CPU (Loihi+CPU hybrid).**

## Limitations / caveats
- Binary spikes require autoencoding interface — adds model complexity.
- Surrogate gradients introduce approximation in training.
- Data-driven (not physics-constrained) — may require retraining for unseen sequences.
- Energy savings depend on spike sparsity of the specific problem.
- Longer training time for spiking vs. second-generation model due to sparse gradient.

## Contradictions with existing wiki
- This paper uses SNNs as stand-alone surrogates; subsequent papers (CMAME 2024, EWCO 2024) embed SNNs inside FEM implicit integration schemes — a significant methodological step beyond this work.
- Note: deployed on **Loihi** (1st gen); CMAME 2024 uses **Xylo-Av2**, NPJ 2026 uses **Loihi 2**.

## Quotes worth keeping
> "The inclusion of spikes enables the proposed model to be deployed on neuromorphic hardware, such as the Loihi chip."

> "A reduction factor of 21.2775 and 1663.878 per epoch is observed when the SLMU layers and decoder are deployed on Loihi and the dense layers are deployed on GPU and CPU respectively."

## See also
[[concepts/spiking-neural-networks]], [[concepts/neuromorphic-computing]], [[concepts/sustainable-ai]], [[concepts/recurrent-neural-networks-in-mechanics]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[Saurabh Balkrishna Tandale]]
