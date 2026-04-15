---
title: "Source: Spiking recurrent neural networks for neuromorphic computing in nonlinear structural mechanics"
type: source
tags: [SNN, neuromorphic, LMU, Loihi, spike, autoencoding, shock-wave, plates, CMAME, sustainable-AI]
created: 2026-04-06
updated: 2026-04-06
sources: 1
---
**First introduction of Spiking Neural Networks (third-generation) into solid mechanics BVPs, proposing a hybrid spiking model with LMU and autoencoding for shock wave-loaded plates, deployed on the Loihi neuromorphic chip.**

## Key points
- Marks the pivot from second-generation (RNN/LSTM) to **third-generation (SNN)** neural networks in Tandale's research.
- SNNs are proposed as surrogate models for **nonlinear structural mechanics** — to the best of the authors' knowledge, the first such application in solid mechanics.
- Hybrid model: **spiking Legendre Memory Unit (LMU)** + spiking recurrent cells + classical dense transformations.
- **Autoencoding strategy**: dense encoder converts real-valued FEM data to binary spikes; dense decoder converts output spikes back to real values.
- Deployed on **Intel Loihi** neuromorphic chip; energy consumption compared with equivalent second-generation RNN.
- Application: shock wave-loaded plate elements (physically and geometrically nonlinear response).

## Methodology / approach
1. Real-valued input sequences (pressure, deformation data) encoded to binary spike trains by a dense encoder.
2. Spiking LMU + spiking recurrent layers process the spike sequence.
3. Dense decoder recovers real-valued displacement prediction.
4. Trained using surrogate gradients (to handle non-differentiable spike function).
5. Deployed on Loihi; energy measured and compared.

## Key claims & evidence
- SNN achieves comparable accuracy to second-generation RNNs for predicting shock wave plate response.
- Energy consumption on Loihi is significantly lower than GPU-based second-generation counterpart.
- Spiking LMU handles the long-range dependencies needed for structural dynamics sequences.

## Limitations / caveats
- Binary spikes require autoencoding interface — adds model complexity.
- Surrogate gradients introduce approximation in training.
- Energy savings depend heavily on the neuromorphic chip and spike sparsity of the specific problem.

## Contradictions with existing wiki
- This paper demonstrates SNNs as stand-alone surrogates for structural response prediction; subsequent papers (CMAME 2024, EWCO 2024) embed SNNs directly inside FEM implicit integration schemes — a significant step beyond this work.

## Quotes worth keeping
> "The inclusion of spikes enables the proposed model to be deployed on neuromorphic hardware, such as the Loihi chip."

## See also
[[concepts/spiking-neural-networks]], [[concepts/neuromorphic-computing]], [[concepts/sustainable-ai]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[Saurabh Balkrishna Tandale]]
