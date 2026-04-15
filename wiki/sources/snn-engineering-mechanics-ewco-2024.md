---
title: "Source: Brain-inspired spiking neural networks in Engineering Mechanics: a new physics-based self-learning framework for sustainable Finite Element analysis"
type: source
tags: [SNN, LMU, spiking-LMU, pseudo-explicit, FEM, viscoplasticity, Xylo, neuromorphic, sustainable, EWCO]
created: 2026-04-06
updated: 2026-04-07
sources: 1
---
**Introduces a pseudo-explicit SNN integration scheme using a spiking LMU, achieving >40% FEM acceleration and energy reduction to 1/1000 on Xylo-Av2 neuromorphic hardware.**

## Key points
- A **pseudo-explicit** integration scheme: although embedded in an implicit FEM solver, the pretrained SNN requires almost no or zero online training steps for unseen loading — effectively explicit in practice.
- Key innovation: **third-generation spike-based Legendre Memory Unit (spiking LMU)** for handling large sequences with memory efficiency.
- Physics-informed strategy: the underlying differential equations are solved using a physics-based loss.
- **Self-learning**: online training capability retained; few or no iterations needed after good pretraining.
- Deployed on **Xylo-Av2 SynSense** neuromorphic chip.
- Application: plate structures under cyclic loading.

## Methodology / approach
1. **Spiking LMU**: Legendre Memory Unit recast in spiking form — handles long cyclic loading sequences efficiently.
2. **Pseudo-explicit integration**: the spike-based model is pretrained so well that deployment requires near-zero online gradient steps.
3. **Neuromorphic deployment**: Xylo-Av2 used to evaluate energy consumption.
4. Physics-based loss enforces viscoplastic constitutive equations during both pretraining and (if needed) online learning.

## Key claims & evidence
- **>40% acceleration** vs. classical FEM simulations.
- **Energy consumption reduced to the thousandth order** (~1/1000) vs. equivalent second-generation networks on conventional hardware.
- Online training capability confirmed for unseen loading sequences.
- Spiking LMU handles sequences significantly longer than conventional LSTM/GRU without memory bottleneck.

## Xylo-Av2 deployment details
- **Toolchain**: Rockpool (initialise spiking layers) → Samna (deploy weights to chip). KerasSpiking v0.3.1 for energy benchmarking.
- **Supported neurons on chip**: LIF (feedforward), RLIF (recurrent) — spiking LMU layers run on CPU; only standard LIF/RLIF components are pushed to hardware.
- **Chip I/O limits**: max 16 input features (autoencoding LIF must compress to 16 neurons); max 8 output features.
- **Input format**: binary spikes only — real-valued FEM data spike-encoded by CPU-side autoencoder before chip receives it.
- **No on-chip training**: the SPEIS pseudo-explicit scheme means near-zero online steps in practice; any gradient updates run on CPU, not on chip.
- **Energy (Section 6.4)**: spiking RLIF layers alone — Xylo 8 nJ vs GPU 892 nJ (111× reduction) vs CPU 25,289 nJ (3,161× reduction). Total hybrid: 1,440 nJ vs 2,325 nJ GPU-only. This underpins the "thousandth order" claim in the abstract (1/1000 vs CPU for spiking layers).

## Limitations / caveats
- Pseudo-explicit behaviour relies on quality of pretraining — poor pretraining requires more online steps.
- Plate structures only; 3D application not demonstrated.

## Contradictions with existing wiki
- EWCO 2024 shows 40% speedup vs CMAME 2024's 30%+ — the spiking LMU and pseudo-explicit scheme appear more efficient than LIF/RLIF in CMAME 2024, though different BVPs are used so direct comparison is not straightforward.

## Quotes worth keeping
> "The proposed framework, although implicit, is viewed as a pseudo-explicit scheme since it requires almost no or fewer online training steps to achieve a converged solution even for unseen loading sequences."

## See also
[[concepts/spiking-neural-networks]], [[concepts/self-learning-nn]], [[concepts/neuromorphic-computing]], [[concepts/sustainable-ai]], [[concepts/viscoplasticity-modelling]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/rnn-plane-stress-damage-mrc-2024]], [[Saurabh Balkrishna Tandale]]
