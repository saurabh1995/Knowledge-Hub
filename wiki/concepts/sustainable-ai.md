---
title: "Sustainable AI"
type: concept
tags: [sustainability, energy, neuromorphic, SNN, green-computing, carbon-footprint]
created: 2026-04-06
updated: 2026-04-06
---
**The pursuit of AI methods that achieve comparable task performance to conventional deep learning while consuming substantially less energy and memory.**

## Motivation
Second-generation deep neural networks (DNNs) require large matrix multiplications at every forward pass, leading to high energy demand. As AI scales, its carbon footprint and infrastructure cost have become significant concerns. Sustainable AI seeks algorithmic and hardware solutions to reduce this footprint.

## Strategies

### Pruning and sparsification (2nd-gen approaches)
- Dropping unnecessary neurons and synaptic connections.
- Reduces energy but provides only moderate savings compared to 3rd-gen approaches.

### Spiking Neural Networks (3rd-gen)
- Communication via sparse binary spikes: energy consumed only on spike events.
- Inherently suited to neuromorphic hardware.
- Energy savings of orders of magnitude over GPU-based DNNs.
- See [[concepts/spiking-neural-networks]].

### Neuromorphic hardware
- Chips like Loihi 2 and Xylo-Av2 are designed for SNN workloads.
- Operate at milliwatt power levels vs. kilowatt-scale GPU servers.
- See [[concepts/neuromorphic-computing]].

## Tandale's contributions to sustainable AI
The body of work from CMAME 2023 onward explicitly frames SNN-based FEM as a contribution to sustainable engineering simulation:

| Paper | Key sustainability result |
|-------|--------------------------|
| CMAME 2023 | First SNN surrogate for BVPs in solid mechanics; energy comparison with 2nd-gen |
| CMAME 2024 | >30% simulation speedup + self-learning SNN on Xylo-Av2 |
| EWCO 2024 | Energy reduction to ~1/1000 vs 2nd-gen; >40% FEM speedup |
| NPJ 2024 | General SNN regression framework on neuromorphic processors |

## See also
[[concepts/spiking-neural-networks]], [[concepts/neuromorphic-computing]], [[sources/spiking-rnn-neuromorphic-cmame-2023]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/snn-nonlinear-regression-neuromorphic-npj]]
