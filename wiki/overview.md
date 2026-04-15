---
title: "Overview"
type: overview
tags: [synthesis, computational-mechanics, FEM, SNN, neuromorphic, self-learning]
created: 2026-04-06
updated: 2026-04-07
---
**Evolving synthesis of all knowledge in this KnowledgeHub — covering Saurabh Tandale's body of work on neural network-enhanced FEM, neuromorphic computing, FPGA acceleration, and CNN-based cell imaging.**

## The big picture

This wiki documents a coherent 5-year research programme (2021–2026) by [[Saurabh Balkrishna Tandale]] at RWTH Aachen University. The unifying goal: **make nonlinear Finite Element Method (FEM) simulations faster and more energy-efficient by replacing expensive classical subroutines with trained neural networks**.

The programme evolves through three distinct phases:

---

## Phase 1 — Smart stiffness (2021–2022)

**Papers:** MRC 2021, IJNME 2022, PAMM 2022

The entry point: replace the tangent stiffness matrix in FEM with a neural network. Classical Newton–Raphson iterations are eliminated because the NN directly outputs the *converged* internal force and stiffness for each element increment.

Key invention: **[[concepts/sobolev-training]]** — an enhanced loss function that trains the NN on both the function (force) and its derivative (stiffness) simultaneously. This is what makes the stiffness prediction accurate enough for FEM deployment.

Results: up to 90.6% simulation speedup on academic plate and beam problems. Break-even (training cost amortised) after ~10 simulations.

---

## Phase 2 — Physics-based self-learning (2022–2024)

**Papers:** CMAME 2022, ABME 2023, CM 2023, MRC 2024

The programme expands from stiffness replacement to full material integration. The key new idea: **[[concepts/physics-informed-neural-networks]]** embedded in the NN loss so the model learns (and continues learning) from physical laws rather than purely from labelled data.

The CMAME 2022 **REIIS** framework is the breakthrough: an LSTM+FFNN model replaces the entire implicit integration (return-mapping) for a viscoplastic Lemaitre–Chaboche material. The model self-updates its weights *during* FEM simulation using the constitutive law residual as the loss — no new labelled data needed.

Parallel development:
- **CM 2023**: benchmarks LSTM, GRU, TCN, and attention encoder-decoder for shock wave-loaded plates. Attention encoder-decoder wins; attention weights have physical interpretability.
- **ABME 2023**: applies RNNs to experimental biomechanics (lumbar spine cyclic loading) — shows the methods transfer outside FEM.
- **MRC 2024**: LMU-based self-learning solver for plane stress viscoplasticity with isotropic damage; explicitly distinguishes self-learning from standard PINNs.

---

## Phase 3 — Neuromorphic and sustainable AI (2023–2026)

**Papers:** CMAME 2023, CMAME 2024, EWCO 2024, NPJ 2024, NPJ 2026

The pivot: second-generation networks (LSTM, GRU) are replaced by **[[concepts/spiking-neural-networks]]** (third-generation), which communicate through sparse binary signals and can be deployed on energy-efficient **[[concepts/neuromorphic-computing|neuromorphic chips]]** (Loihi, Xylo-Av2).

Key progression:
- **CMAME 2023**: first SNN surrogate for solid mechanics BVPs; introduces spiking LMU + autoencoding strategy; deployed on Loihi.
- **CMAME 2024**: SNN (LIF+RLIF) embedded directly into FEM implicit integration as a self-learning plastic corrector; >30% speedup, deployed on Xylo-Av2.
- **EWCO 2024**: spike-based LMU + pseudo-explicit integration scheme; >40% speedup; energy reduced to 1/1000 on Xylo-Av2.
- **NPJ 2024** (Stoffel & Tandale): general SNN regression framework for complex transient signals on neuromorphic processors.
- **NPJ 2026**: introduces Hybrid Spiking Neurons (HSN) — real-valued spiking — and applies **[[concepts/meta-learning-maml|MAML]]** meta-learning for better pretraining initialisation; deployed on Loihi 2 with QAT.

---

## Key conceptual threads

| Concept | Appears across |
|---------|---------------|
| [[concepts/neural-network-enhanced-fem]] | All 12 papers |
| [[concepts/sobolev-training]] | MRC 2021, IJNME 2022, PAMM 2022 |
| [[concepts/physics-informed-neural-networks]] | CMAME 2022, MRC 2024, CMAME 2024, EWCO 2024 |
| [[concepts/self-learning-nn]] | CMAME 2022, MRC 2024, CMAME 2024, EWCO 2024, NPJ 2026 |
| [[concepts/recurrent-neural-networks-in-mechanics]] | CMAME 2022, PAMM 2022, ABME 2023, CM 2023, MRC 2024 |
| [[concepts/spiking-neural-networks]] | CMAME 2023, CMAME 2024, EWCO 2024, NPJ 2024, NPJ 2026 |
| [[concepts/neuromorphic-computing]] | CMAME 2023, CMAME 2024, EWCO 2024, NPJ 2024, NPJ 2026 |
| [[concepts/sustainable-ai]] | CMAME 2023, CMAME 2024, EWCO 2024, NPJ 2024 |
| [[concepts/viscoplasticity-modelling]] | CMAME 2022, CMAME 2024, EWCO 2024, MRC 2024, NPJ 2026 |
| [[concepts/meta-learning-maml]] | NPJ 2026 |

---

---

## Phase 4 — FPGA acceleration and cell imaging (2020–2025, parallel threads)

Two parallel research directions expand the scope beyond the FEM-SNN core:

### 4a — FPGA + Binary Neural Networks
**Paper:** MRC 2025 ([[sources/fpga-bnn-viscoplastic-mrc-2025]])

A third hardware pathway is introduced: [[concepts/binary-neural-networks]] (BNNs) deployed on FPGA (PYNQ Z2, Xilinx). BNNs replace floating-point multiplications with 1-bit XNOR-popcount operations, which map efficiently to FPGA logic resources. The BNN replaces the Lemaitre–Chaboche viscoplastic law at Gauss points with a hybrid encoder (real) → binary core → decoder (real) architecture.

Result: FPGA BNN forward pass is 60% faster than Intel i7 CPU and 26% faster than NVIDIA RTX 4090 GPU. Energy comparison deferred (PYNQ Z2 lacks on-chip power measurement).

Key conceptual addition: **FPGA vs. neuromorphic comparison**. FPGAs are reconfigurable and support any NN architecture; neuromorphic ASICs (Loihi, Xylo) are fixed to SNNs but achieve ~1/1000 energy for spiking workloads. The two paradigms are complementary.

### 4b — CNN for cell imaging (non-invasive regenerative medicine)
**Papers:** CDBME 2020, CMPB 2021 ([[sources/cnn-tenogenic-differentiation-cmpb-2021]]), PAMM 2024 ([[sources/cell-preserving-chondrocyte-pamm-2024]])

CNNs applied to phase-contrast microscopy as a replacement for invasive immunostaining:
- **Tenogenic differentiation**: 4 CNN architectures classify BMSCs vs. tenocytes vs. chondrocytes; best model (Inception-ResNet V2 inspired, IE + DA) achieves >91% accuracy; deployed on Android via TensorFlow Lite
- **Chondrocyte dedifferentiation**: YoloV8 instance segmentation detects intact vs. early/late dedifferentiated CHs in tensile bioreactor experiments; cells remain alive → enables longitudinal mechanobiological study

This thread is an adjacent collaboration (led by Gözde Dursun and Hyun Lee), not core to the FEM programme, but demonstrates the group's broader competency in applied machine learning for biomedical sensing.

### The PhD dissertation as consolidation (2024)
**Source:** [[sources/dissertation-sustainable-brain-inspired-2024]]

The 240-page dissertation (defended Sept 12, 2024) unifies all work from Phases 1–3 into a single coherent framework with a shared theoretical background (Chapters 2–4) and systematic experimental evidence. It does not include the FPGA (Phase 4a) or cell imaging (Phase 4b) threads, which are collaborative satellite works.

---

## Key conceptual threads

| Concept | Appears across |
|---------|---------------|
| [[concepts/neural-network-enhanced-fem]] | All FEM papers (17 sources) |
| [[concepts/sobolev-training]] | MRC 2021, IJNME 2022, PAMM 2022 |
| [[concepts/physics-informed-neural-networks]] | CMAME 2022, MRC 2024, CMAME 2024, EWCO 2024 |
| [[concepts/self-learning-nn]] | CMAME 2022, MRC 2024, CMAME 2024, EWCO 2024, NPJ 2026 |
| [[concepts/recurrent-neural-networks-in-mechanics]] | CMAME 2022, PAMM 2022, ABME 2023, CM 2023, MRC 2024 |
| [[concepts/spiking-neural-networks]] | CMAME 2023, CMAME 2024, EWCO 2024, NPJ 2024, NPJ 2026 |
| [[concepts/neuromorphic-computing]] | CMAME 2023, CMAME 2024, EWCO 2024, NPJ 2024, NPJ 2026 |
| [[concepts/sustainable-ai]] | CMAME 2023, CMAME 2024, EWCO 2024, NPJ 2024 |
| [[concepts/viscoplasticity-modelling]] | CMAME 2022, CMAME 2024, EWCO 2024, MRC 2024, NPJ 2026, MRC 2025 |
| [[concepts/meta-learning-maml]] | NPJ 2026 |
| [[concepts/binary-neural-networks]] | MRC 2025 |
| [[concepts/fpga-acceleration-nn]] | MRC 2025 |
| [[concepts/cnn-cell-imaging]] | CDBME 2020, CMPB 2021, PAMM 2024 |

---

## Open questions / frontiers (as of 2026-04-07)
1. **3D generalisation**: all FEM validations are on plate elements; 3D solid elements are untouched.
2. **Complex geometries**: academic BVPs dominate; real engineering structures not yet demonstrated.
3. **Other materials**: viscoplasticity (Lemaitre–Chaboche) dominates; hyperelasticity, damage-only, multi-physics not yet covered.
4. **Attention + FEM**: the CM 2023 attention encoder-decoder is not yet embedded in an FEM solver.
5. **Generalised meta-task design**: what constitutes a good task distribution for MAML in FEM contexts?
6. **FPGA energy benchmarking**: PYNQ Z2 lacks power monitoring; future FPGAs needed for full energy comparison with neuromorphic chips.
7. **Cell imaging → mechanobiological model**: the chondrocyte dedifferentiation data (PAMM 2024) is a first step; a quantitative mechanoregulatory model is the stated goal.

## See also
[[index]]
