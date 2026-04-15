---
title: "Recurrent Neural Networks in Mechanics"
type: concept
tags: [RNN, LSTM, GRU, LMU, path-dependence, history, structural-mechanics, surrogate]
created: 2026-04-06
updated: 2026-04-06
---
**The use of recurrent neural network architectures (LSTM, GRU, LMU) as surrogate models in mechanics, where path-dependent material behaviour requires tracking loading history.**

## Why RNNs for mechanics?
Feed-forward NNs are memoryless — they cannot capture path-dependent behaviour (plasticity, viscoplasticity, creep) without explicitly providing history variables. RNNs carry an internal hidden state that evolves with the input sequence, making them natural surrogates for:
- History-dependent constitutive laws
- Cyclic loading responses
- Dynamic structural sequences

## Architectures used in Tandale's work

### LSTM (Long Short-Term Memory)
- Gates (input, forget, output) control information flow across time steps.
- Used in: CMAME 2022 (REIIS), PAMM 2022 (stiffness plate), ABME 2023 (spine biomechanics), CM 2023 (shock waves).
- Captures path-dependent viscoplastic and viscoelastic behaviour.

### GRU (Gated Recurrent Unit)
- Simpler variant of LSTM with fewer parameters.
- Used in CM 2023 (comparison study). GRU encoder-decoder with attention showed best performance for shock wave plate response.

### LMU (Legendre Memory Unit)
- Uses orthogonal polynomials (Legendre basis) to compress long input sequences into a fixed-size memory buffer.
- Handles longer sequences more efficiently than LSTM/GRU.
- Used in MRC 2024 (plane stress viscoplasticity + isotropic damage) and EWCO 2024 (spiking LMU variant).

### TCN (Temporal Convolutional Network)
- Dilated causal convolutions; parallelisable but less suited to very long path-dependent sequences.
- Used in CM 2023 (comparison only).

## Comparison (CM 2023, shock wave-loaded plates)
| Architecture                    | Relative performance                          |
| ------------------------------- | --------------------------------------------- |
| LSTM                            | Good baseline                                 |
| GRU                             | Comparable to LSTM                            |
| TCN                             | Faster training; less accurate for long paths |
| GRU encoder-decoder + attention | **Best overall**                              |

## Spiking variants
LMU has been extended to a **spiking LMU** (third-generation) in EWCO 2024 and NPJ 2024, enabling deployment on neuromorphic hardware. See [[concepts/spiking-neural-networks]].

## See also
[[concepts/neural-network-enhanced-fem]], [[concepts/physics-informed-neural-networks]], [[concepts/self-learning-nn]], [[concepts/attention-mechanism]], [[concepts/spiking-neural-networks]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[sources/lstm-stiffness-plate-pamm-2022]], [[sources/lumbar-spine-biomechanics-rnn-abme-2023]], [[sources/rnn-cnn-shock-wave-plates-cm-2023]], [[sources/rnn-plane-stress-damage-mrc-2024]]
