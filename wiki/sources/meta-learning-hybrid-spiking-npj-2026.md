---
title: "Source: Meta-learning Hybrid Spiking networks as physics-based nonlinear solvers for physical simulations"
type: source
tags: [meta-learning, MAML, HSN, hybrid-spiking, Loihi-2, QAT, FEM, viscoplasticity, NPJ, 2026]
created: 2026-04-06
updated: 2026-04-15
sources: 1
---
**Introduces Hybrid Spiking Neurons (HSNs) — real-valued spiking neurons — meta-pretrained with MAML for physics-based FEM solvers, with Quantization-Aware Training for Loihi 2 deployment; outperforms first-order pretrained baselines.**

## Key points
- Journal: *npj Unconventional Computing* 3:3 (2026). DOI: 10.1038/s44335-025-00048-y
- Authors: Tandale, Stoffel — RWTH Aachen University
- **Hybrid Spiking Neuron (HSN)**: outputs **real-valued** membrane potential when active, zero when silent — combines 3rd-gen sparsity with 2nd-gen regression capability.
- On Loihi 2: HSNs use 32-bit integer outputs — more suitable for regression than binary LIF.
- **MAML** (Model-Agnostic Meta-Learning): second-order gradient meta-learning pretrains HSNN weights for rapid adaptation to new loading paths with minimal online steps.
- **QAT** (Quantization-Aware Training): prepares weights for 32-bit integer deployment on Loihi 2.
- **Self-learning**: inner MAML loop acts as online self-learning during FEM deployment using physics loss only.
- BVPs: viscoplastic plate simulations with geometrical and physical nonlinearity (Aluminum).

## HSN neuron equations (NPJ 2026, Eq. 9–12)

$$V^t_{l,(d)} = \beta_{l,(d)}\, V^{t-1}_{l,(d)} + \sum_j W_{lj,(d)}\, z^t_{j,(d-1)} - \varphi_s(V^{t-1}_{l,(d)})\, V^{\text{thr}}_{l,(d)}$$

$$S^t_{l,(d)} = \varphi_s(V^t_{l,(d)}), \qquad O^t_{l,(d)} = V^t_{l,(d)} \cdot S^t_{l,(d)}$$

- $S^t$: binary spike; $O^t$: graded output (real-valued potential × spike). Active neurons propagate $V$, silent neurons output 0.
- Learnable: $\{W^{(d)}, \beta_{l,(d)}, V^{\text{thr}}_{l,(d)}\}$.

## QAT quantization (NPJ 2026, Eq. 1)

$$\tilde{m} = \left\lfloor \frac{m - m_{\min}}{s_m} \right\rceil, \qquad s_m = \frac{m_{\max} - m_{\min}}{2^n - 1}$$

**Bit-widths**: 8 bits for weights; 16 bits for integer spiking outputs and membrane potential. Dynamic quantization — scale $s_m$ computed based on incoming data at each forward pass.

**Quantization strategy**: Unlike Post-Training Quantization (PTQ) which can cause catastrophic performance loss, QAT trains the model *with simulated quantization*, allowing weights to adapt to low-precision arithmetic and providing smoother transition from 32-bit float (GPU) to hardware-restricted integer format (Loihi 2).

### Performance impact of QAT

Despite QAT's benefits for hardware deployment, **quantization degrades learning ability**:
- **Non-quantized HSN + MAML**: 60 epochs, achieves convergence at early stopping criterion
- **QAT HSN + MAML**: 100 epochs, ~67% longer training, higher test error

**Mechanism of degradation**: Meta/second-order gradients in MAML amplify biased approximations introduced by two sources: (1) Straight-Through Estimators (STE) used for quantization backprop, and (2) surrogate gradients for spiking neurons. The outer loop meta-gradient update propagates these biased approximations back to the initialized parameters, driving them to regions where performance appears good under biased but worse under true quantized dynamics.

**Hardware-accuracy tradeoff**: Despite accuracy loss, QAT is essential — it ensures the model retains knowledge when deployed on Loihi 2. In deployment, only the final dense layer requires CPU/GPU adaptation; all spiking layers run inference-only on the neuromorphic chip.

## Training details

- Inner loop: 1 gradient step per task; 100 training sequences ($D_{\text{tr}}$).
- Outer loop: up to 100 meta-epochs; 250 validation sequences ($D_{\text{val}}$).
- Early stopping: $|\nabla_\theta \mathcal{L}| < 10^{-5}$ or test MSE $< 10^{-4}$.
- Averaged over 8 trials.
- Physics loss (Eq. 8): yield function residual of the backward-Euler-integrated Lemaitre-Chaboche law.

## Comparative results (Table 1)

| Model | Outer loop loss | Inner loop loss | Test loss | Epochs to stop |
|-------|----------------|-----------------|-----------|---------------|
| RNN (with 10% dropout) | 2.3e-2 | 2.7e-2 | 9.71e-3 | 59 (early stop) |
| **HSN + MAML (proposed, non-quantized)** | 1.6e-2 | 2.31e-2 | **4.381e-2** | 60 (early stop) |
| RNN (no dropout) | 3.6e-3 | 1.57e-2 | 1.8e-2 | 100 |
| SNN + MAML | 1.18e-2 | 2.4e-2 | 7.81e-3 | 100 |
| **QAT HSN + MAML (quantized, 8-bit weights, 16-bit outputs)** | 1.44e-2 | 3.78e-2 | higher | 100 |

**Key observation**: HSNN with MAML (non-quantized) achieves best balance of accuracy and energy efficiency. QAT causes **40 epoch increase** and **higher test loss** due to meta/second-order gradient amplification of quantization bias.

## Speedup and iteration reduction (Tables 2–3)

| BVP | GP in inelastic domain | Global iterations | Pegasus iterations at GP | NN iterations at GP | Speedup |
|-----|----------------------|-------------------|--------------------------|---------------------|---------|
| BVP1 (Fig. 6) | 72 | 541 | 7 845 | 5 056 | ~19% |
| BVP2 (Fig. 7) | 86 | 742 | 16 593 | 13 258 | ~7.3% |

Speedup is conjectural (Python in-house solver). More complex BVPs with larger inelastic zones expected to show greater gains.

## Methodology / approach
1. **HSN neuron**: each neuron outputs real-valued state when active, zero when silent.
2. **Meta-pretraining**: MAML outer loop optimises θ so that 1 inner gradient step adapts to any new FE task. Tasks = different loading sequences.
3. **QAT**: 32-bit integer quantization for Loihi 2.
4. **FE deployment**: meta-pretrained HSNN replaces Newton-Raphson/Pegasus at Gauss points. Inner loop self-learning triggered on physics residual. Outer loop with pretraining sequences runs after each time increment to prevent catastrophic forgetting.

## Limitations / caveats
- Second-order MAML is computationally expensive at pretraining time.
- **QAT performance penalty**: Quantization-Aware Training causes ~67% increase in outer loop epochs and higher test loss vs. non-quantized HSN. This is a known tradeoff between neuromorphic hardware deployment and training-time accuracy. Mechanism: meta-gradients amplify biased approximations from STE and surrogate gradients.
- Validated on plate BVPs only; 3D not demonstrated.
- Speedup gains modest at this BVP scale; larger inelastic zones needed to show full potential.
- Task distribution design (which loading sequences to meta-train on) has no principled method yet.

## Quotes worth keeping
> "We show that Hybrid Spiking Neural Networks (HSNNs) meta-pretrained by combined physics-based and data-driven loss terms outperform HSNNs pretrained with standard first-order methods."

> "Through meta-learning, transfer learning is cast as a generalization problem, and we found that only the last layer is required to adapt to reach the desired convergence criteria."

## See also
[[concepts/meta-learning-maml]], [[concepts/spiking-neural-networks]], [[concepts/self-learning-nn]], [[concepts/neuromorphic-computing]], [[concepts/physics-informed-neural-networks]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[Saurabh Balkrishna Tandale]]
