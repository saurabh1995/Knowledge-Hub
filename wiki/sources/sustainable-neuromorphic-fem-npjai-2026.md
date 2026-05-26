---
title: "Source: Introducing Sustainable Neuromorphic Computing in Engineering Mechanics"
type: source
tags: [SNN, neuromorphic, FEM, GNN, DI-GNS, FPGA, energy-efficiency, sustainable-AI, SLMU, viscoplasticity, CO2, npj-AI]
created: 2026-05-26
updated: 2026-05-26
sources: 1
---
**Flagship synthesis paper introducing a three-tier sustainable framework for FEM: (i) GNN-based FEM surrogate (DI-GNS), (ii) physics-based self-learning SNN at Gaussian points, (iii) FPGA-deployed QNN/BNN — with quantified CO₂ savings for a real crash test simulation.**

## Key points
- Establishes a complete three-tier neuromorphic FEM framework spanning data-driven GNN surrogates (complete BVP replacement), physics-based SNN at Gauss points (generalised self-learning solver), and FPGA-deployed BNNs (reconfigurable hardware path).
- GNN crash simulation (non-spiking): CPU 14 h × 105 W ≈ 5.29 kWh vs GNN 1.25 J — >99% energy reduction; introduces DI-GNS (Dynamics Informed Graph Network-based Solver) using GATv2Conv and physics-conditioning loss.
- Hybrid SLSTM+dense network for single FE elements: 303.4 kWh → 4 kWh per simulation (115 kg CO₂ → 4 kg CO₂) for a 4.2M-element, 100,000-time-step crash test.
- Physics-based self-learning SNN in Gaussian points (Loihi): 14.3 nJ vs 435,488 nJ on i7 per inference; saves 5.1 kg CO₂; with SpMV on FPGA → 92% total energy reduction for complete crash simulation.
- Xylo chip material law inference: Xylo 8.0 nJ vs GPU 892.4 nJ vs CPU 25,289 nJ; also validates SNN deployment via SynSense Speck and Loihi emulators.

## Methodology / approach

### DI-GNS (Dynamics Informed Graph Network-based Solver)
GATv2Conv graph attention network; processes FEM mesh as graph (nodes = FE nodes, edges = element connectivity). Physics-conditioning hybrid loss: data-driven MSE + constitutive residual (σ = C:εᵉ) + L2 regularisation. Encoder: aggregates features → GNN → spatial decoder. Predicts full field variables (stress, strain, displacement) for any BVP with the trained structural type.

### SLMU (Spiking Legendre Memory Unit)
New formulation: LMU memory cell (continuous-delay ODE state-space) wrapped with ALIF/LIF spiking hidden state instead of dense RNN cell. Combines LMU long-memory capacity with SNN sparsity. Used in the hybrid autoencoding network (Fig. 5) and in the single-element surrogate network.

### Self-learning SNN in Gaussian points
LIF + RLIF neurons at Gauss points; viscoplastic Lemaitre–Chaboche law integrated in weak form as physics loss (scalar residual L(Δεₚ) = 0 drives online adaptation). Hybrid loss: data-driven Ld during pre-training + physics-based Lp during FE deployment (λ₁=0, λ₂=1 in FE solve). Self-learning reaches converged solution in one step vs. classical FEM iterative approach.

### FPGA BNN
FINN framework; hybrid encoder (real) → 2 binary layers → decoder (real); PYNQ Z2 ZYNQ FPGA board; XNOR-popcount replaces MAC operations. Surrogates for viscoplastic material law in impulsively loaded beams.

### Hardware / tools
Intel Loihi (KerasSpiking emulator); SynSense Xylo (Rockpool + Samna); SynSense Speck; FPGA PYNQ Z2 (Xilinx ZYNQ Z2); GPU RTX 3090 (training); CPU AMD Ryzen 7 5800X (GNN benchmark); Intel Xeon 8468 Sapphire CPU server (crash simulation).

## Key claims & evidence

| Claim | Value | Location |
|-------|-------|----------|
| Loihi vs CPU: matrix-vector multiplication energy ratio | 1/300,000 | Fig. 1b/c, pp.1-2 |
| GNN crash simulation energy | 1.25 J vs 5.29 kWh (FEM 14h @ 105W) | p.3 |
| GNN energy reduction vs FEM | >99% | p.3 |
| SLSTM-dense total energy (single element sim) | 8.3×10⁴ nJ vs LSTM-dense 2.577×10⁶ nJ | Table 1, p.4 |
| SLSTM-dense simulation energy | 4 kWh vs LSTM-dense 303.4 kWh | Table 1, p.4 |
| CO₂ savings (SLSTM hybrid, crash sim) | 115 kg → 4 kg CO₂ | p.5, Table 1 |
| SNN Gauss-point inference energy (Loihi) | 14.3 nJ vs 435,488 nJ on i7 | Table 2, p.7 |
| SNN Gauss-point CO₂ savings | 5.1 kg CO₂ (of 12 kg total) | p.7 |
| Total energy reduction (SNN Gauss + FPGA SpMV) | 92% | p.7, Fig. 9c |
| Energy reduction (SNN Gauss only, dense layers excluded) | 43% | p.6 |
| Xylo vs CPU for material law inference | 8.0 nJ vs 25,289 nJ (3,161×) | Fig. 12, p.9 |
| Xylo vs GPU | 8.0 nJ vs 892.4 nJ (111.5×) | Fig. 12, p.9 |
| FPGA BNN forward pass time | 1.34 ms (FPGA) vs 1.83 ms (GPU) vs 3.41 ms (CPU) | p.8 |
| FPGA BNN vs CPU speedup | 60% faster | p.8 |
| FPGA BNN vs RTX 4090 speedup | 26% faster | p.8 |
| Self-learning SNN: 1 step to converge | vs classical iterative FEM | Fig. 10-11, pp.7-8 |

## Limitations / caveats
- No live neuromorphic co-processor solution exists; Gaussian-point energy estimates are extrapolated from forward-pass benchmarks, not measured during a full FE run.
- Data-driven GNN approach is the most energy-saving but is BVP-specific; physics-based SNN approach is general but less energy-saving.
- Xylo hardware constraints limit hyperparameter choices (LIF/RLIF neurons only; ≤16 inputs, ≤8 outputs on chip).
- Energy estimates for the crash simulation assume average per-element energy holds across all element types and loading states.
- FPGA energy consumption not measured (PYNQ Z2 lacks on-chip power monitoring) — speed comparison only.

## Contradictions with existing wiki
- The Xylo energy figures (CPU 25,289 nJ, GPU 892.4 nJ, Xylo 8.0 nJ) match the values already in [[concepts/neuromorphic-computing]], which attributes them to EWCO 2024 / CMAME 2024. This paper cites those results [ref 43] for the same deployment. No contradiction — same benchmark, re-cited in synthesis.
- The FPGA BNN speedup (60% vs CPU, 26% vs RTX 4090) matches [[sources/fpga-bnn-viscoplastic-mrc-2025]], which reports the same figures. This paper cites [ref 14] (= MRC 2025). No contradiction — same result from a companion paper.

## Quotes worth keeping
> "We introduce a sustainable neuromorphic approach for numerical simulations in Engineering Mechanics."

> "We anticipate our study to be a starting point for more sustainable AI models in engineering science and related disciplines."

> "The required energy is reduced by several orders of magnitude compared to classical numerical simulations, which can significantly lower CO₂ emissions in time-consuming computations."

## See also
[[concepts/neuromorphic-computing]], [[concepts/neural-network-enhanced-fem]], [[concepts/spiking-neural-networks]], [[concepts/sustainable-ai]], [[concepts/fpga-acceleration-nn]], [[concepts/self-learning-nn]], [[concepts/viscoplasticity-modelling]], [[Marcus Stoffel]], [[Rutwik Gulakala]], [[Vasileios Polydoras]], [[Saurabh Balkrishna Tandale]], [[sources/spiking-rnn-neuromorphic-cmame-2023]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/fpga-bnn-viscoplastic-mrc-2025]]
