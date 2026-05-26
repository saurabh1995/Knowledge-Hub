---
title: "Source: A Biologically Plausible Recurrent Spiking Architecture for Modeling Nonlinear Dynamic Mechanical Systems"
type: source
tags: [SNN, RSNN, ALIF, e-prop, LMU, recurrent, biologically-plausible, viscoelastic, shock-tube, CMAME]
created: 2026-05-26
updated: 2026-05-26
sources: 1
---
**First application of the biologically plausible e-prop learning rule to multi-layer RSNNs for nonlinear mechanical regression, establishing a physical analogy between viscoelastic rheological models and LIF neuron dynamics.**

## Key points
- Establishes a mathematical analogy between viscoelastic rheological models (Maxwell element + damper) and LIF membrane dynamics — both are first-order linear ODEs with leaky integration and input-driven terms, providing physical justification for SNNs in mechanics beyond energy efficiency.
- Trains a two-layer ALIF-based RSNN on a viscoelastic dataset using both BPTT and the biologically plausible e-prop learning rule; e-prop enables online, memory-efficient learning without storing all hidden states across time.
- Proposes a multi-layer LMU + ALIF RSNN for shock tube plate deformation prediction (15,000-step sequences), using a hybrid e-prop/BPTT scheme (e-prop for recurrent hidden states; BPTT for LMU memory and dense layers).
- BPTT consistently achieves lower MSE and faster convergence than e-prop; however, e-prop's memory usage does not scale with sequence length — making it attractive for neuromorphic deployment.
- For the rheological dataset, spiking BPTT outperforms the non-spiking baseline (MSE 0.00165 vs 0.00335), consistent with the structural analogy; on the more complex shock tube dataset the non-spiking model performs better.

## Methodology / approach

### ALIF neuron
Adaptive-threshold Leaky Integrate-and-Fire: extends LIF with a dynamic threshold that increases after each spike and decays back exponentially. Adds intrinsic temporal memory beyond the membrane potential alone.

### Architectures
- **Architecture 1 (rheological dataset):** 2 RSNN (ALIF) layers, 128 neurons each, 5-feature input (strain rate, its derivative ε̈, E₁, η₀, η₁); spike-trains from each layer fed through temporal filters to separate readout weights.
- **Architecture 2 (shock tube):** 3 LMU-RSNN layers (memory dimension d=50, window θ=500, 50 ALIF neurons each) + 4 dense (tanh) layers; input is pressure history P; outputs are concatenated and mapped to midpoint displacement.

### Learning rules
- **BPTT:** exact gradients unrolled through all time steps; requires storing all hidden states.
- **e-prop:** local online rule; decomposes gradient into learning signal L and eligibility trace e per synapse; no historical state storage; weight updates computed at each time step.
- **Hybrid:** e-prop for ALIF recurrent cells; BPTT for LMU memory matrices and dense layers.

### Datasets
- Rheological: 3-element Maxwell model, two material parameter sets (E₁=600 Pa/800 Pa, η₁=10/20 Pa·s, η₂=5/10 Pa·s), 1000 time steps, stress predicted from step-function strain-rate input.
- Shock tube: aluminium/steel plate under impulsive load (Stoffel 2001–2006 experiments); ~15,000 time steps at 1 MHz; segmented into 500-step windows; 80-10-10 train/val/test split; ~1000 total samples.

### Implementation
TensorFlow; Adam optimiser; lr=1×10⁻³; batch size=16; loss=MSE; hardware: NVIDIA RTX 5000 Ada Generation GPU.

## Key claims & evidence

| Claim | Value | Location |
|-------|-------|----------|
| BPTT MSE test (rheological) | 0.00165 (180 epochs) | Table B.2, p.19 |
| e-prop MSE test (rheological) | 0.00273 (440 epochs) | Table B.2, p.19 |
| Non-spiking MSE test (rheological) | 0.00335 (230 epochs) | Table B.2, p.19 |
| BPTT MSE test (shock tube) | 0.0155 (940 epochs) | Table B.2, p.19 |
| e-prop hybrid MSE test (shock tube) | 0.0376 (1220 epochs) | Table B.2, p.19 |
| Non-spiking MSE test (shock tube) | 0.0222 (720 epochs) | Table B.2, p.19 |
| Spiking (BPTT) > non-spiking on rheological | MSE 0.00165 vs 0.00335 | Sec 5.3, Table B.2 |
| Non-spiking > spiking on shock tube | MSE 0.0222 vs 0.0376 (test) | Sec 5.3, Table B.2 |
| e-prop epochs overhead vs BPTT | ~2.4× (440 vs 180 for rheological) | Table B.2, p.19 |
| e-prop memory: does not scale with sequence length | qualitative | Sec 5.2.1, p.12 |
| Physical analogy: rheological ODE ≡ LIF ODE | Both first-order leaky-integrator ODEs | Sec 3.1, p.7; Eq. (32) vs Eq. (1) |

## Limitations / caveats
- e-prop consistently requires more epochs and yields higher MSE than BPTT; the gain is in online/memory-efficient learning, not accuracy.
- Hybrid e-prop/BPTT for LMU-RSNN is a preliminary exploration; full e-prop for the LMU memory component is not yet implemented.
- No hardware energy measurements — all training on GPU; neuromorphic deployment of e-prop-trained networks is motivated but not demonstrated here.
- SNN advantage over non-spiking holds only when target system physics structurally resembles LIF dynamics; the shock tube result shows this does not generalise.
- Surrogate gradient (fast sigmoid) used throughout; gradient quality is approximate.

## Contradictions with existing wiki
None. This paper is the first to introduce e-prop and ALIF into this wiki. Results are consistent with existing SNN pages: LMU + spiking is effective for mechanics regression (CMAME 2023 established this), and BPTT-trained SNNs can match or outperform non-spiking baselines on structurally aligned tasks.

## Quotes worth keeping
> "The structural analogy provides a physical justification for employing spiking neurons in mechanics, extending their relevance beyond energy efficiency to a framework grounded in similar governing equations."

> "Although inspired by biological neural systems, most existing SNNs bear only limited resemblance to their biological counterparts in terms of dynamics and learning mechanisms."

## See also
[[concepts/e-prop]], [[concepts/spiking-neural-networks]], [[concepts/recurrent-neural-networks-in-mechanics]], [[sources/spiking-rnn-neuromorphic-cmame-2023]], [[sources/snn-nonlinear-regression-neuromorphic-npj]], [[Vaishnav Bhaskaran]], [[Saurabh Balkrishna Tandale]], [[Vasileios Polydoras]], [[Marcus Stoffel]]
