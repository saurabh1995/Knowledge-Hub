---
title: "Source: Physics-Based Self-Learning Spiking Neural Network enhanced time-integration scheme for computing viscoplastic structural finite element response"
type: source
tags: [SNN, LIF, RLIF, self-learning, viscoplasticity, FEM, implicit-integration, Xylo, neuromorphic, CMAME]
created: 2026-04-06
updated: 2026-04-07
sources: 1
---
**Embeds a physics-based self-learning Spiking Neural Network (LIF + RLIF neurons) into the plastic corrector step of FEM implicit integration for viscoplastic plates, achieving >30% speedup and deploying on Xylo-Av2 neuromorphic chip.**

## Key points
- Direct FEM integration: unlike CMAME 2023 (stand-alone surrogate), the SNN is embedded inside the FE implicit integration scheme as the plastic corrector.
- Hybrid model: **LIF** (Leaky Integrate-and-Fire) + **RLIF** (Recurrent LIF) neurons + dense layers.
- **Autoencoding strategy** to convert real-valued FEM state variables to spike representation.
- **Self-learning**: the model is first pretrained offline with combined data-driven and physics-based loss, then continues online training during FE simulation using only the physics-based loss — no labelled data required during deployment.
- Deployed on **Xylo-Av2** (SynSense) neuromorphic chip; energy performance measured.
- BVPs: plate elements with physically and geometrically nonlinear viscoplastic material.

## Methodology / approach
1. **Pretraining**: combined data-driven + physics-based loss (constitutive equation residuals of viscoplastic model).
2. **Deployment**: the pretrained SNN replaces the plastic corrector step. During each FE increment, online gradient steps are taken if the physics residual is above tolerance.
3. **Self-learning loop**: physics loss drives weight updates without new labelled data.
4. **Neuromorphic evaluation**: spiking layers deployed on Xylo-Av2; energy compared with second-generation RNN.

## Key claims & evidence
- **>30% overall computational gain** vs. classical FEM.
- Self-learning ability bolsters convergence — the model adapts to the specific loading path being solved.
- LIF/RLIF neurons deployed on Xylo-Av2 demonstrate substantially lower energy consumption.
- Two major advantages: (1) computational speed, (2) online adaptability.

## Xylo-Av2 deployment details
- **Toolchain**: Rockpool (network initialisation) → Samna (chip flash/deploy). KerasSpiking v0.3.1 used for energy profiling.
- **Supported neurons on chip**: LIF (feedforward layers), RLIF (recurrent layers) — no other types.
- **Chip I/O limits**: max 16 input features; max 8 output features — constrains autoencoding layer sizes.
- **Input format**: binary spikes only; CPU-side autoencoding LIF layer performs the real-value → spike conversion.
- **No on-chip training**: all gradient updates (offline pretraining + online self-learning) run on CPU/GPU; deployed weights are read-only on chip.
- **Split inference path**: CPU (autoencoding) → Xylo-Av2 (RLIF + decoder LIF) → CPU (dense output layers).
- **Energy (Table 4/5)**: spiking RLIF layers — Xylo 8 nJ vs GPU 892 nJ (111× reduction) vs CPU 25,289 nJ (3,161× reduction). Full hybrid model: 1,440 nJ (Xylo+GPU) vs 2,325 nJ (GPU-only).

## Limitations / caveats
- Plate elements only; 3D generalisation not demonstrated.
- Energy advantage depends on spike sparsity of the specific viscoplastic problem.
- Two-stage training (offline pretraining + online self-learning) adds complexity.

## Quotes worth keeping
> "Two major advantages were observed: an overall computational gain in the excess of 30% and the self-learning/online training ability of the model that bolsters its convergence behavior."

## See also
[[concepts/spiking-neural-networks]], [[concepts/self-learning-nn]], [[concepts/physics-informed-neural-networks]], [[concepts/viscoplasticity-modelling]], [[concepts/neuromorphic-computing]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[Saurabh Balkrishna Tandale]]
