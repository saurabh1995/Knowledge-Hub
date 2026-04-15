---
title: "Source: FPGA-Accelerated Binary Neural Networks for Viscoplastic FEM (MRC 2025)"
type: source
tags: [FPGA, BNN, binary-neural-network, viscoplasticity, FEM, hardware-acceleration, Lemaitre-Chaboche, PYNQ]
created: 2026-04-07
updated: 2026-04-07
sources: 1
---
**Binarized Neural Networks (BNNs) replace the Lemaitre–Chaboche viscoplastic constitutive law in FEM; the BNN forward pass runs on a PYNQ Z2 FPGA board at 60% faster speed than a CPU and 26% faster than an RTX 4090 GPU.**

## Key points
- Journal: *Mechanics Research Communications* 146 (2025) 104420
- Authors: Vasileios Polydoras, Saurabh Balkrishna Tandale, Rutwik Gulakala, Marcus Stoffel — RWTH Aachen University
- First application of BNNs and FPGA hardware to viscoplastic FEM surrogate modelling
- Two impulsive boundary value problems (IBV): (1) cantilever beam loaded at tip, (2) beam supported at both ends loaded at midpoint — steel, Lemaitre–Chaboche material

## Material parameters (Table 1) — source: Stoffel (2005) *ZAMM* 85(9)

| Parameter | Steel |
|-----------|-------|
| E (N/mm²) | 198 600 |
| ρ (kg/m³) | 7 806 |
| k (N/mm²) | 167.88 |
| a (N/mm²) | 2 500 |
| s | 20.30 |
| K (N/mm²) | 63.12 |
| n | 4.22 |

> Note: different steel than CMAME 2022 (E=67 400, k=110). Both sourced from Stoffel 2005 but different experimental campaigns. See [[concepts/lemaitre-chaboche-parameters]] for full comparison.

## BNN architecture (Fig. 7 + Table 2)

| Part | Layers | Type | Activation | Neurons |
|------|--------|------|-----------|---------|
| Encoder | 2 dense + 1 binary | Real → Binary | ReLU / Sign | 100 |
| Core | 1 binary | Binary | Sign | 100 |
| Decoder | 3 dense | Binary → Real | ReLU | 100 |

- **Total: 7 layers** (2 binary + 5 dense — optimal from architecture search, Table 2)
- **Input:** $[\sigma_{11}, \sigma_{12}, X_{11}, X_{12}]$
- **Output:** $[\dot{\varepsilon}^p_{11}, \dot{\varepsilon}^p_{12}, \dot{X}_{11}, \dot{X}_{12}]$
- Binary layers: 1-bit weights/activations (−1/+1), batch norm → sign activation
- Binary dot product: XNOR-popcount (uses ~½ LUT+FF resources vs. multiply-accumulate)
- Hyperparameter tuning: Hyperband over [10, 50, 100, 150, 200, 250, 300] neurons → **100 optimal**
- Training: Adam lr=1e-3 (reduce ×0.8 on plateau, patience=50), batch=64, **2628 epochs**
- Training / validation / test loss: 7.8×10⁻⁴ / 5.4×10⁻⁴ / 7.3×10⁻⁴
- Dataset: 8 698 samples from cantilever BVP Gaussian points in plasticity zone; 80/10/10 split

## Architecture comparison (Table 2)

| Configuration | Training loss |
|--------------|--------------|
| 2 binary + 5 dense (chosen) | **7.8×10⁻⁴** |
| 3 binary + 4 dense | 1.38×10⁻³ |
| 4 binary + 3 dense | 7.67×10⁻³ |

## FEM setup

| Parameter | Value |
|-----------|-------|
| Element type | Timoshenko beam |
| Elements | 40 |
| Gauss points / element | 4 |
| Time integrator | Newmark (β=0.25, γ=0.5) |
| Material integrator | Implicit trapezoid |
| Time step Δt | 10⁻³ s |
| Total timesteps | 800 |
| IBV 1 | Cantilever, F=80 kN at tip, L=5 m, 0.2×0.2 m |
| IBV 2 | Fixed-fixed, F=0.4 MN at midpoint, same geometry |

## FPGA deployment (FINN library, Xilinx)

Workflow: Trained BNN → Streamlining → HW layers → Dataflow partition → HLS variants → Folding (PE=SIMD=10) → Bitfile (PYNQ Z2)

- **MVTU** (Matrix-Vector-Threshold Unit): main compute block per binary layer
- Streamlining collapses batch norm + sign into a single threshold: 6 LUTs vs 2 DSPs+55 FFs+40 LUTs

## Hardware timing (Table 4) — two binary layers forward pass

| Hardware | Time (ms) |
|----------|-----------|
| **PYNQ Z2 FPGA** | **1.34** |
| Intel i7-13700K CPU | 3.41 |
| Mac M1 CPU | 5.33 |
| NVIDIA RTX 4090 GPU | 1.83 |
| NVIDIA RTX 3090 GPU | 2.00 |

FPGA: **60% faster than CPU, 26% faster than RTX 4090**. Energy not benchmarkable (PYNQ Z2 has no on-chip power monitoring).

## Accuracy (Table 3) — scaled MSE vs. FEM reference

| IBV | Displacement u | Strain ε | Stress σ |
|-----|---------------|---------|---------|
| 1st (cantilever, training domain) | 6.16×10⁻⁶ | 7.65×10⁻⁵ | 1.44×10⁻⁴ |
| 2nd (fixed-fixed, unseen BVP) | 4.03×10⁻⁴ | 1.24×10⁻³ | 6.41×10⁻⁴ |

## Limitations / caveats
- Energy consumption not measured (PYNQ Z2 no on-chip power monitoring)
- Binary layers introduce noise in stress-time and stress-strain curves (accuracy trade-off)
- Only 1D beam elements; plate/3D not demonstrated
- FINN toolchain is Xilinx-specific (not portable without rework)
- Trained on step-function impact loads only

## Contradictions with existing wiki
- Two distinct steel parameter sets in the wiki: this paper E=198 600 MPa vs. CMAME 2022 E=67 400 MPa. See [[concepts/lemaitre-chaboche-parameters]].
- FPGA is more flexible than neuromorphic chips (Loihi, Xylo) but lacks the 1/1000 energy advantage of Xylo-Av2 (EWCO 2024).

## Quotes worth keeping
> "BNNs allow for effective programming of Field Programmable Gate Arrays (FPGAs), enabling the efficient execution of BNNs forward passes in terms of computation time and energy consumption."

> "Unlike general-purpose processors (e.g. CPUs) or Application-Specific Integrated Circuits (ASICs) (e.g. neuromorphic chips), which are hard-wired for specific tasks, FPGAs can be reprogrammed post-manufacturing to adapt to different applications."

## See also
[[Saurabh Balkrishna Tandale]], [[concepts/binary-neural-networks]], [[concepts/fpga-acceleration-nn]], [[concepts/neuromorphic-computing]], [[concepts/viscoplasticity-modelling]], [[concepts/lemaitre-chaboche-parameters]], [[concepts/neural-network-enhanced-fem]]
