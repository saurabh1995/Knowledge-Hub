---
title: "Source: Spiking neural networks for nonlinear regression of complex transient signals on sustainable neuromorphic processors"
type: source
tags: [SNN, LMU, spiking-LMU, regression, neuromorphic, autoencoding, transient-signals, NPJ, sustainable]
created: 2026-04-06
updated: 2026-04-19
sources: 1
---
**Proposes a general SNN regression framework for complex transient signal processing, with a spiking LMU for path-dependence and an autoencoding interface, validated on experimental wave propagation and inelastic deformation benchmarks on neuromorphic processors.**

## Key points
- **Lead author: Marcus Stoffel** (Tandale is second author — reversal of usual order).
- Addresses a gap: SNN literature is dominated by classification; this paper provides a **general regression framework** for SNNs.
- Key motivation: nonlinear regression (function approximation) on neuromorphic processors is largely unexplored despite high potential.
- Spiking LMU for memory and path-dependent signal processing.
- **Autoencoding** interface: dense encoder → spiking layers → dense decoder, to convert between real-valued signals and binary spike representations.
- Surrogate gradients used for training (spike function is non-differentiable).
- Published in *npj Unconventional Computing* (uncorrected proof in Tandale's files).

## Methodology / approach
1. **Hybrid architecture**: dense autoencoder wrapping a spiking LMU core.
2. **Experimental benchmarks**: high-frequency oscillations measured by capacitive and piezoelectric sensors — wave propagations and inelastic solid deformations.
3. Framework tested for prediction of complex short-time transient signals.
4. Deployed on neuromorphic processors; energy performance evaluated.

## Key claims & evidence
- General SNN regression framework applicable to a wide range of scientific and technical applications.
- Spiking LMU handles path-dependencies and signal evolutions effectively in the spiking domain.
- Framework validated on realistic, experimentally measured benchmarks (not just simulated data).
- **Energy benchmark (Table 3, Layer 1)**: GPU = 3,434 nJ · CPU = 99,081 nJ · Loihi = **5.4072 nJ** — Loihi is 18,331× more energy-efficient than CPU for this layer.
- **Total model energy reduction (Table 4)**:
  - CPU / Loihi = **35,581.4×**
  - GPU / Loihi = 1,176.33×
  - Hybrid SLMU + CPU = 1,663.9×
  - Hybrid SLMU + GPU = 21.28×
- Hardware used: Intel Loihi (neuromorphic), NVIDIA GTX Titan Black (GPU), Intel Core i7-4960X (CPU).
- The hybrid SLMU+CPU/GPU figures are for models where spiking layers run on Loihi and dense encoder/decoder layers run on conventional hardware — representative of practical deployment scenarios.

## Limitations / caveats
- Uncorrected proof — may have minor changes in published version.
- Regression framework is general but validation is on specific experimental setups (wave propagation, inelastic deformation).
- Energy figures depend on specific neuromorphic hardware used.

## Quotes worth keeping
> "We believe that there is a need for a general regression framework for SNNs to explore the high potential of neuromorphic computations."

## See also
[[concepts/spiking-neural-networks]], [[concepts/neuromorphic-computing]], [[concepts/sustainable-ai]], [[sources/spiking-rnn-neuromorphic-cmame-2023]], [[sources/meta-learning-hybrid-spiking-npj-2026]], [[Saurabh Balkrishna Tandale]]
