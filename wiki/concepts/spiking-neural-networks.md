---
title: "Spiking Neural Networks"
type: concept
tags: [SNN, neuromorphic, third-generation, LIF, RLIF, LMU, energy-efficiency, spikes]
created: 2026-04-06
updated: 2026-04-06
---
**Third-generation neural networks that communicate through discrete spike signals, enabling energy-efficient deployment on neuromorphic hardware.**

## Generations of neural networks
| Generation | Type | Signal | Hardware |
|-----------|------|--------|----------|
| 1st | Perceptrons | Continuous, rate-coded | CPU |
| 2nd | Deep ANNs (FFNN, CNN, RNN) | Continuous, floating-point | GPU/CPU |
| **3rd** | **Spiking NNs (SNNs)** | **Binary spike trains** | **Neuromorphic chips** |

## Why spikes save energy
- Neurons fire only when their membrane potential crosses a threshold — otherwise they are silent.
- Silence means no matrix multiplications, no memory access.
- Energy is consumed only when a spike propagates through the network.
- Result: orders-of-magnitude less energy than second-generation equivalents.

## Key neuron models

### LIF (Leaky Integrate-and-Fire)
- Standard spiking neuron: membrane potential leaks toward rest, fires (binary spike = 1) when threshold exceeded.
- Used in: CMAME 2023, CMAME 2024.

### RLIF (Recurrent LIF)
- LIF with recurrent connections — models temporal dependencies.
- Used in: CMAME 2024.

### Spiking LMU
- Spiking variant of the Legendre Memory Unit — compresses long sequences into a memory buffer using spike-based communication.
- Used in: EWCO 2024, NPJ 2024.

### HSN (Hybrid Spiking Neuron)
- New model introduced in NPJ 2026: unlike LIF, outputs a **real-valued** (not binary) activation when active, and zero when inactive.
- Combines sparsity of third-generation models with the continuous output needed for regression tasks.
- Deployable on Loihi 2 (32-bit integer outputs).
- Used in: NPJ 2026.

## Autoencoding strategy
Because SNNs produce binary outputs, a hybrid autoencoding approach is used to interface with real-valued FEM data:
- **Encoder**: dense (2nd-gen) layer converts real-valued input to spike representation.
- **Spiking core**: processes the spike sequence.
- **Decoder**: dense layer converts spikes back to real-valued output.
Used in: CMAME 2023, CMAME 2024, EWCO 2024, NPJ 2024.

## Hardware deployment in Tandale's work
| Paper | Chip | Key metric |
|-------|------|-----------|
| CMAME 2023 | Loihi | Energy comparison vs 2nd gen |
| CMAME 2024 | Xylo-Av2 (SynSense) | Energy performance |
| EWCO 2024 | Xylo-Av2 (SynSense) | Energy reduction to ~1/1000 |
| NPJ 2026 | Loihi 2 | QAT for integer deployment |

## See also
[[concepts/neuromorphic-computing]], [[concepts/sustainable-ai]], [[concepts/self-learning-nn]], [[concepts/recurrent-neural-networks-in-mechanics]], [[sources/spiking-rnn-neuromorphic-cmame-2023]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/snn-nonlinear-regression-neuromorphic-npj]], [[sources/meta-learning-hybrid-spiking-npj-2026]]
