---
title: "E-prop"
type: concept
tags: [e-prop, eligibility-propagation, biologically-plausible, learning-rule, RSNN, ALIF, online-learning, neuromorphic]
created: 2026-05-26
updated: 2026-05-26
---
**Eligibility propagation (e-prop): a biologically plausible, local, online learning rule for recurrent spiking neural networks that approximates BPTT using neuron-level eligibility traces and broadcast error signals, enabling weight updates without storing all historical states.**

## Motivation: limitations of BPTT for SNNs

Standard backpropagation through time (BPTT) for RSNNs suffers from two properties:

- **Non-locality:** gradient of a synapse Wⱼᵢ depends on the future states of all other neurons k ≠ j — not just the local pre/post-synaptic activity.
- **Non-causality:** gradients at time t require information from all future time steps; requires storing the entire hidden-state trajectory (memory complexity O(nT) for n neurons and T time steps).

These properties make BPTT biologically implausible and unsuitable for real-time or neuromorphic deployment.

## Core idea of e-prop

E-prop replaces the full gradient decomposition with a product of two locally available quantities:

$$\frac{dE}{dW_{ji}} = \sum_t L^t_j \cdot e^t_{ji}$$

- $L^t_j$ — **learning signal**: broadcast error term computed from the prediction loss and transmitted to each neuron (analogous to neuromodulatory signals in the brain).
- $e^t_{ji}$ — **eligibility trace**: a synapse-local running integral of recent pre/post-synaptic activity; integrates the history of the synapse without storing all states globally.

The eligibility trace is updated recursively at each time step:

$$\boldsymbol{\epsilon}^{t+1}_{ji} = \frac{\partial \mathbf{h}^{t+1}_j}{\partial \mathbf{h}^t_j} \cdot \boldsymbol{\epsilon}^t_{ji} + \frac{\partial \mathbf{h}^{t+1}_j}{\partial W_{ji}}$$

This makes e-prop **causal** (only past activity used) and **local** (only information available at synapse Wⱼᵢ).

## ALIF eligibility trace

For ALIF neurons with 2D hidden state $\mathbf{h}^t_j = [v^t_j,\, a^t_j]$, the eligibility vector is 2D: $\boldsymbol{\epsilon}^t_{ji} = [\epsilon^t_{ji,v},\, \epsilon^t_{ji,a}]$. Its update:

$$\begin{bmatrix} \epsilon^{t+1}_{ji,v} \\ \epsilon^{t+1}_{ji,a} \end{bmatrix} = \begin{bmatrix} \alpha & 0 \\ \psi^t_j & \rho - \psi^t_j\beta \end{bmatrix} \begin{bmatrix} \epsilon^t_{ji,v} \\ \epsilon^t_{ji,a} \end{bmatrix} + \begin{bmatrix} z^t_i \\ 0 \end{bmatrix}$$

- $\alpha = e^{-\delta t/\tau_m}$: membrane decay; $\rho = e^{-\delta t/\tau_a}$: adaptation decay; $\beta$: adaptation strength.
- $\psi^t_j$: pseudo-derivative of the spiking function (fast sigmoid surrogate gradient).
- The final eligibility trace: $e^t_{ji} = \psi^t_j\bigl(\bar{z}^{t-1}_i - \beta\,\epsilon^t_{ji,a}\bigr)$ where $\bar{z}^{t-1}_i = \alpha(\bar{z}^{t-2}_i) + z^{t-1}_i$ is a low-pass-filtered presynaptic spike train.

## Multi-layer extension

For $L$ layers, e-prop assigns separate learning signals and eligibility traces per layer:

$$\frac{dE}{dW_{ji}} = \sum_{t=1}^T \sum_{l=1}^L L^{t,l}_j\, e^{t,l}_{ji}$$

## Hybrid e-prop / BPTT

In [[sources/biologically-plausible-rsnn-cmame-2026]], a hybrid scheme is used for the LMU-RSNN architecture:
- **e-prop** trains the recurrent ALIF hidden states (online, local).
- **BPTT** trains the LMU memory matrices and dense decoder layers (exact gradients, non-causal parts).

This allows the biologically-plausible component to benefit from e-prop while retaining BPTT accuracy for the non-spiking parts.

## Performance trade-offs vs BPTT

| Property | BPTT | e-prop |
|---------|------|--------|
| Gradient accuracy | Exact | Approximate (locality approximation) |
| Memory scaling | O(nT) — scales with sequence length | O(n) — constant in time |
| Convergence speed | Faster | ~2.4× more epochs needed |
| MSE accuracy | Lower | Slightly higher |
| Online/real-time learning | No | Yes |
| Neuromorphic-compatible | No | Yes |
| Biological plausibility | No | Yes |

Key numbers from [[sources/biologically-plausible-rsnn-cmame-2026]] (Table B.2, p.19):
- Rheological: e-prop 440 epochs / MSE 0.00273 vs BPTT 180 epochs / MSE 0.00165
- Shock tube: e-prop hybrid 1220 epochs / MSE 0.0376 vs BPTT 940 epochs / MSE 0.0155

## Hardware relevance

E-prop has been demonstrated on neuromorphic hardware (SpiNNaker 2) in the neuroscience literature (Rostami et al. 2022, cited in Bhaskaran 2026). Its online and local properties make it the natural training algorithm for on-chip learning on neuromorphic processors, without CPU-side gradient accumulation.

## Mathematical formulation

Canonical equations from [[sources/biologically-plausible-rsnn-cmame-2026]], Sec 2.3, Eqs. 14–31. Full derivations are in the body sections above.

Variable definitions:
- $E$: loss (MSE); $W_{ji}$: synaptic weight from pre-neuron $i$ to post-neuron $j$
- $z^t_j \in \{0,1\}$: observable spike state of neuron $j$ at time $t$
- $\mathbf{h}^t_j$: hidden state vector (membrane potential + adaptive threshold for ALIF)
- $L^t_j = \partial E / \partial z^t_j$: learning signal (broadcast, not local)
- $e^t_{ji}$: eligibility trace (local to synapse $W_{ji}$)

## See also
[[concepts/spiking-neural-networks]], [[sources/biologically-plausible-rsnn-cmame-2026]], [[concepts/neuromorphic-computing]], [[concepts/sustainable-ai]]
