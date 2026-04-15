---
title: "Recurrent Neural Networks in Mechanics"
type: concept
tags: [RNN, LSTM, GRU, LMU, path-dependence, history, structural-mechanics, surrogate]
created: 2026-04-06
updated: 2026-04-15
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

## Mathematical formulation

### LSTM cell (Hochreiter & Schmidhuber 1997; used in CMAME 2022, PAMM 2022, ABME 2023, CM 2023)

$$f^t = \sigma(W_f h^{t-1} + U_f x^t + b_f) \quad \text{[forget gate]}$$
$$i^t = \sigma(W_i h^{t-1} + U_i x^t + b_i) \quad \text{[input gate]}$$
$$\tilde{c}^t = \tanh(W_c h^{t-1} + U_c x^t + b_c) \quad \text{[candidate cell]}$$
$$c^t = f^t \odot c^{t-1} + i^t \odot \tilde{c}^t \quad \text{[cell state]}$$
$$o^t = \sigma(W_o h^{t-1} + U_o x^t + b_o) \quad \text{[output gate]}$$
$$h^t = o^t \odot \tanh(c^t) \quad \text{[hidden state / output]}$$

- $\sigma$: sigmoid; $\odot$: element-wise multiplication; $h^t$: hidden state output fed to the next layer.
- In CMAME 2022 (REIIS): 2 LSTM layers × 128 units; input $= [\hat{\sigma}', \hat{X}, k, K]$; output $= \Delta\dot{\bar{\varepsilon}}^p$.

### LMU state-space form (Voelker et al. 2019; used in CMAME 2023, MRC 2024, EWCO 2024)

**Continuous-time memory ODE:**

$$\dot{m}(t) = A\, m(t) + B\, u(t)$$

**Discrete-time update (ZOH discretisation):**

$$m^t = \bar{A}\, m^{t-1} + \bar{B}\, u^t, \qquad h^t = e_x\, x^t + e_m\, m^t$$

- $m(t) \in \mathbb{R}^q$: memory state ($q = 100$ in CMAME 2023).
- $A \in \mathbb{R}^{q \times q}$, $B \in \mathbb{R}^{q \times 1}$: derived from Padé approximants of the Laplace delay $e^{-\theta s}$.
- $e_x, e_m$: learnable encoding weights; $u^t$: scalar input at time $t$.
- **Key advantage over LSTM**: memory capacity is independent of the number of hidden neurons — a smaller LMU layer can hold the same memory as a larger LSTM.

## See also
[[concepts/neural-network-enhanced-fem]], [[concepts/physics-informed-neural-networks]], [[concepts/self-learning-nn]], [[concepts/attention-mechanism]], [[concepts/spiking-neural-networks]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[sources/lstm-stiffness-plate-pamm-2022]], [[sources/lumbar-spine-biomechanics-rnn-abme-2023]], [[sources/rnn-cnn-shock-wave-plates-cm-2023]], [[sources/rnn-plane-stress-damage-mrc-2024]]
