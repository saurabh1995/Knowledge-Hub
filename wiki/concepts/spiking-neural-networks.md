---
title: "Spiking Neural Networks"
type: concept
tags: [SNN, neuromorphic, third-generation, LIF, RLIF, LMU, energy-efficiency, spikes]
created: 2026-04-06
updated: 2026-05-26
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

### ALIF (Adaptive-threshold Leaky Integrate-and-Fire)
- Extends LIF with a dynamic threshold: after each spike, the threshold A rises and decays back exponentially to baseline $v_{\text{th}}$ over time constant $\tau_a$.
- Two-dimensional hidden state: membrane potential $v^t_j$ and threshold adaptation $a^t_j$.
- Provides richer temporal memory than LIF — threshold history acts as an intrinsic memory trace.
- Used in: CMAME 2026 (Bhaskaran et al.) for both single-layer RSNN and LMU-RSNN architectures.
- Dynamics: $v^{t+1}_j = \alpha v^t_j + \sum_{i\neq j} W^{\text{rec}}_{ji} z^t_i + \sum_i W^{\text{in}}_{ji} x^{t+1}_i - z^t_j v_{\text{th}}$; threshold $A^t_j = v_{\text{th}} + \beta a^t_j$; adaptation $a^{t+1}_j = \rho a^t_j + z^t_j$.

### HSN (Hybrid Spiking Neuron)
- New model introduced in NPJ 2026: unlike LIF, outputs a **real-valued** (not binary) activation when active, and zero when inactive.
- Combines sparsity of third-generation models with the continuous output needed for regression tasks.
- Deployable on Loihi 2 (32-bit integer outputs).
- Used in: NPJ 2026.

## Learning rules for SNNs

| Rule | Type | Memory complexity | Biologically plausible | Used in |
|------|------|------------------|----------------------|---------|
| BPTT | Global, non-causal | O(nT) | No | All papers up to CMAME 2026 |
| e-prop | Local, online, causal | O(n) | Yes | CMAME 2026 (Bhaskaran) |

E-prop uses eligibility traces (local synapse-level memory) and broadcast learning signals rather than backpropagating global gradients. Trade-off: more epochs needed but memory does not scale with sequence length. See [[concepts/e-prop]] for full formulation.

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
| CMAME 2026 | RTX 5000 Ada (training only) | e-prop + hybrid BPTT; ALIF + LMU-RSNN |
| npj AI 2026 | Loihi / Xylo / Speck / FPGA | Multi-chip energy benchmarking for FEM |

## Mathematical formulation

### LIF neuron (CMAME 2023, Eq. 1–2; CMAME 2024, Eq. 34–35)

Discrete-time membrane potential update (forward Euler of the RC-circuit ODE):

$$V^t_{a,(l)} = \beta_{a,(l)}\, V^{t-1}_{a,(l)} + W_{a,(l)}\, z^t_{l-1} - (\varphi_s)^{t-1}_{(l)}\, V^{\text{thr}}_{a,(l)}$$

Fire condition and reset:

$$\varphi_s = \begin{cases} 1 & V^t_{a,(l)} \ge V^{\text{thr}}_{a,(l)} \\ 0 & V^t_{a,(l)} < V^{\text{thr}}_{a,(l)} \end{cases}$$

- $\beta_{a,(l)}$: membrane decay rate (learnable); $W_{a,(l)}$: input weights; $z^t_{l-1}$: previous layer output; $V^{\text{thr}}$: threshold potential (learnable).
- The reset term $(\varphi_s)^{t-1} V^{\text{thr}}$ fires when the threshold is crossed; otherwise the neuron integrates without firing.
- Learnable parameters: $\Theta_{\text{LIF}} = \{W_{a,(l)},\, \beta_{a,(l)},\, V^{\text{thr}}_{a,(l)}\}$.

### RLIF neuron (CMAME 2024)

Same as LIF but with an added recurrent weight $R^{(t)}$ on the previous spiking output:

$$V^t_{a,(l)} = \beta\, V^{t-1}_{a,(l)} + W\, z^t_{l-1} + R^{(t)}\, (\varphi_s)^{t-1}_{a,(l)} - (\varphi_s)^{t-1}\, V^{\text{thr}}$$

Learnable parameters: $\Theta_{\text{RLIF}} = \{W^{(t)},\, R^{(t)},\, \beta,\, V^{\text{thr}}\}$.

### Spiking LMU (CMAME 2023, Eq. 7–8)

The Legendre Memory Unit compresses long sequences via a state-space ODE:

$$\dot{m}(t) = A\, m(t) + B\, u(t), \qquad m^t = \bar{A}\, m^{t-1} + \bar{B}\, u^t$$

- $m(t) \in \mathbb{R}^n$: memory state vector of dimension $n$ (100 in CMAME 2023).
- $A \in \mathbb{R}^{n \times n}$, $B \in \mathbb{R}^{n \times 1}$: derived from Padé approximants of the delay transfer function.
- $\bar{A}, \bar{B}$: zero-order hold (ZOH) discretisation.
- The spiking variant (SLMU) passes the memory through spike thresholding before propagation.
- Working memory: $h^t = e_x\, x^t + e_m\, m^t$ where $e_x, e_m$ are learnable scalars.

### Hybrid Spiking Neuron — HSN (NPJ 2026, Eq. 9–12)

Unlike LIF (binary output), HSN propagates the real-valued membrane potential of active neurons:

$$V^t_{l,(d)} = \beta_{l,(d)}\, V^{t-1}_{l,(d)} + \sum_j W_{lj,(d)}\, z^t_{j,(d-1)} - \varphi_s(V^{t-1}_{l,(d)})\, V^{\text{thr}}_{l,(d)}$$

$$S^t_{l,(d)} = \varphi_s(V^t_{l,(d)}), \qquad O^t_{l,(d)} = V^t_{l,(d)} \cdot S^t_{l,(d)}$$

- $S^t$: binary spike (0 or 1); $O^t$: graded output — real-valued potential if active, zero if silent.
- This combines 3rd-gen sparsity with 2nd-gen continuous-value regression capability.
- Learnable: $\Theta_{\text{HSN}} = \{W^{(d)},\, \beta_{l,(d)},\, V^{\text{thr}}_{l,(d)}\}$.
- On Loihi 2: implemented with 32-bit integer outputs.

### Surrogate gradient (all SNN variants)

The non-differentiable $\varphi_s$ is replaced by the arcus tangent surrogate during backpropagation:

$$\widetilde{\varphi}_s'(V) = \frac{1}{\pi}\frac{1}{1+(V\pi)^2}$$

## See also
[[concepts/neuromorphic-computing]], [[concepts/sustainable-ai]], [[concepts/self-learning-nn]], [[concepts/recurrent-neural-networks-in-mechanics]], [[concepts/e-prop]], [[sources/spiking-rnn-neuromorphic-cmame-2023]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/snn-nonlinear-regression-neuromorphic-npj]], [[sources/meta-learning-hybrid-spiking-npj-2026]], [[sources/biologically-plausible-rsnn-cmame-2026]], [[sources/sustainable-neuromorphic-fem-npjai-2026]]
