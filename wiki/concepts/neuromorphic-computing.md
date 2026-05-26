---
title: "Neuromorphic Computing"
type: concept
tags: [neuromorphic, hardware, Loihi, Xylo, SpiNNaker, brain-inspired, energy-efficiency]
created: 2026-04-06
updated: 2026-05-26
---

**Brain-inspired computing architectures that process information through spike-based signals, achieving significantly lower energy and memory consumption than conventional von Neumann processors.**

## Principle
Neuromorphic chips mimic biological neural dynamics:
- Small units (silicon neurons) integrate incoming spikes over time.
- A neuron fires only when its membrane potential crosses a threshold.
- Between spikes, synaptic weights and memory remain inaccessible — no energy is consumed.
- This event-driven, sparse communication contrasts with the dense matrix multiplications of GPU-based deep learning.

## Key chips referenced in Tandale's work
| Chip | Maker | Used in |
|------|-------|---------|
| Loihi | Intel | CMAME 2023 |
| Loihi 2 | Intel | NPJ 2026 |
| Xylo-Av2 | SynSense | CMAME 2024, EWCO 2024 |
| SpiNNaker | Univ. Manchester | Mentioned CMAME 2023 |
| Speck | SynSense | npj AI 2026 (CNN/SNN alternative to Xylo) |

## Energy advantage (EWCO 2024)
The SNN framework deployed on Xylo-Av2 showed energy consumption reduced to the **thousandth order** compared to equivalent second-generation networks on standard hardware.

## Deployment requirements
To run on neuromorphic hardware, networks must:
1. Use spiking neuron models (LIF, RLIF, spiking LMU, HSN).
2. Quantize weights — Quantization-Aware Training (QAT) used in NPJ 2026 for Loihi 2's 32-bit integer format.
3. Use autoencoding interfaces to convert real-valued engineering data to/from spike representations.

## Xylo-Av2 deployment (SynSense)

### Toolchain
| Tool | Role |
|------|------|
| **Rockpool** (SynSense Python lib) | Define network architecture; initialise spiking layers (LIF/RLIF) with trained weights |
| **Samna** (SynSense device SDK) | Compile and flash weights onto the physical Xylo-Av2 chip; manage chip I/O |
| **KerasSpiking v0.3.1** | Power profiling and energy estimation for comparison benchmarks |

### Hardware constraints
| Constraint | Value | Implication |
|-----------|-------|-------------|
| Supported neuron types | LIF (feedforward), RLIF (recurrent) | No spiking LMU or HSN on chip |
| Max chip input features | 16 | Autoencoding layer must compress FEM state to ≤16 binary neurons |
| Max chip output features | 8 | Decoder layer output capped at 8 real-valued features |
| Input signal type | Binary spikes only | Real-valued data must be spike-encoded on CPU before sending to chip |
| On-chip training | Not supported | All weight updates (offline pretraining + self-learning) run on CPU/GPU |

### Deployment workflow (6 steps)
1. **Design** the hybrid network: autoencoding LIF (CPU) → RLIF/LIF spiking core (chip) → dense decoder (CPU).
2. **Pretrain** offline on CPU/GPU using combined data-driven + physics-based loss.
3. **Initialise** the spiking layers in Rockpool, loading the pretrained weights.
4. **Deploy** spiking-layer weights to Xylo-Av2 via Samna.
5. **Run inference**: real-valued FEM inputs → CPU spike encoding → chip RLIF processing → chip LIF decoder → CPU dense layers → FEM output.
6. **Self-learning** (if needed): only dense layers on CPU receive gradient updates; spiking layers on chip remain frozen.

### Energy benchmarks (EWCO 2024 / CMAME 2024)
Spiking layers only (RLIF/RNN block):

| Platform | Energy per forward pass | Reduction vs GPU |
|---------|------------------------|-----------------|
| Xylo-Av2 | 8 nJ | — (reference) |
| GPU | 892.4 nJ | 111.55× |
| CPU | 25,289 nJ | 3,161× |

Total hybrid model (spiking on Xylo + dense on GPU): **1,440 nJ** vs 2,325 nJ (GPU-only) — 38% total energy saving.

## CO₂ framing (npj AI 2026)

[[sources/sustainable-neuromorphic-fem-npjai-2026]] quantifies neuromorphic savings in CO₂ (using 0.38 kg CO₂/kWh, German Environment Agency):

| Approach | Energy saving | CO₂ saving |
|---------|--------------|------------|
| GNN surrogate (non-spiking) vs FEM CPU | >99% | ~5 kg vs ~5.29 kWh |
| Hybrid SLSTM+dense elements | 303.4 kWh → 4 kWh | 115 kg → 4 kg |
| SNN at Gaussian points (Loihi) | 14.3 nJ vs 435,488 nJ/inference | 5.1 kg saved in crash sim |
| SNN Gauss + SpMV on FPGA | 92% total | — |

Key metric: Loihi vs CPU for matrix-vector multiplication = **1/300,000** energy ratio (Fig. 1, npj AI 2026). This is a pure spiking workload; hybrid networks with dense layers achieve less (see hybrid table above).

## DI-GNS (Dynamics Informed Graph Network-based Solver)

Introduced in [[sources/sustainable-neuromorphic-fem-npjai-2026]]: a second-generation GNN FEM surrogate using GATv2Conv layers with a hybrid loss (data-driven MSE + constitutive physics residual $\|\hat{\sigma} - C:\hat{\varepsilon}^e\|_F^2$). Predicts full field variables for entire BVPs without Gaussian-point evaluation. Most energy-efficient tier but BVP-specific (requires retraining for new geometries/BCs).

## Engineering mechanics context
Neuromorphic computing for FEM is novel — Tandale's papers (from CMAME 2023 onward) are among the first to demonstrate brain-inspired hardware in the context of boundary value problems in solid mechanics. The npj AI 2026 synthesis paper frames the complete three-tier landscape: GNN surrogate (complete BVP replacement) → SNN at Gauss points (generalised self-learning solver) → FPGA BNN (reconfigurable hardware).

## Neuromorphic vs. FPGA
An important distinction introduced by [[sources/fpga-bnn-viscoplastic-mrc-2025]]:
- **Neuromorphic chips (ASICs)** — hard-wired for SNNs; achieve 1/1000 energy advantage for spike-based workloads; not reprogrammable
- **FPGAs** — reconfigurable; support any NN architecture (BNNs, DNNs, CNNs, and in principle SNNs); 60% faster than CPU for BNN inference; energy advantage not yet benchmarked (hardware limitation of PYNQ Z2)
- Both are energy-efficient alternatives to GPU for NN inference in engineering contexts, but serve different NN paradigms

## See also
[[concepts/spiking-neural-networks]], [[concepts/sustainable-ai]], [[concepts/fpga-acceleration-nn]], [[sources/spiking-rnn-neuromorphic-cmame-2023]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/snn-nonlinear-regression-neuromorphic-npj]], [[sources/meta-learning-hybrid-spiking-npj-2026]], [[sources/fpga-bnn-viscoplastic-mrc-2025]], [[sources/sustainable-neuromorphic-fem-npjai-2026]]
