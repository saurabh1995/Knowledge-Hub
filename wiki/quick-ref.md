---
title: "Quick Reference"
type: concept
tags: [reference, lookup, performance]
created: 2026-04-07
updated: 2026-04-10
---
**Flat facts table — primary grep target for queries. Update on every ingest.**

---

## Papers: method, metric, hardware, venue

| Slug | Year | Venue | Architecture | Key metric | Hardware |
|------|------|-------|-------------|-----------|---------|
| smart-stiffness-1d-fem-mrc-2021 | 2021 | MRC | ANN (stiffness replacement) | 41% speedup | CPU |
| intelligent-stiffness-plate-beam-ijnme-2022 | 2022 | IJNME | NN (stiffness replacement) | >90% speedup | CPU |
| physics-based-rnn-viscoplastic-cmame-2022 | 2022 | CMAME | LSTM + FFNN (REIIS) | physics-informed implicit integration | CPU |
| lstm-stiffness-plate-pamm-2022 | 2022 | PAMM | LSTM (stiffness replacement) | 90.6% speedup | CPU |
| lumbar-spine-biomechanics-rnn-abme-2023 | 2023 | ABME | LSTM | R²=0.988 | CPU |
| rnn-cnn-shock-wave-plates-cm-2023 | 2023 | CM | LSTM / GRU / TCN / attention enc-dec | attention wins benchmark | CPU |
| spiking-rnn-neuromorphic-cmame-2023 | 2023 | CMAME | Spiking LMU (autoencoding) | first SNN for solid mechanics BVPs | Intel Loihi |
| spiking-nn-viscoplastic-fem-cmame-2024 | 2024 | CMAME | LIF + RLIF (self-learning) | >30% speedup | Innatera Xylo-Av2 |
| snn-engineering-mechanics-ewco-2024 | 2024 | EWCO | Spiking LMU (pseudo-explicit) | >40% speedup; 1/1000 energy vs CPU | Innatera Xylo-Av2 |
| rnn-plane-stress-damage-mrc-2024 | 2024 | MRC | LMU (self-learning) | plane stress + isotropic damage | CPU (explicit FEM) |
| snn-nonlinear-regression-neuromorphic-npj | 2024 | NPJ Unconv. | Spiking LMU | general transient signal regression | neuromorphic (general) |
| cell-preserving-chondrocyte-pamm-2024 | 2024 | PAMM | YoloV8 | non-invasive chondrocyte classification | CPU/GPU |
| dissertation-sustainable-brain-inspired-2024 | 2024 | PhD Diss. (RWTH) | ANN → RNN → SNN (full framework) | unified FEM surrogate framework | Loihi / Xylo-Av2 |
| fpga-bnn-viscoplastic-mrc-2025 | 2025 | MRC | BNN (binary weights/activations) | 60% faster than CPU; 26% faster than RTX 4090 | FPGA (PYNQ Z2) |
| meta-learning-hybrid-spiking-npj-2026 | 2026 | NPJ Unconv. | HSN + MAML | meta-learned FEM initialisation | Intel Loihi 2 |
| cnn-tenogenic-recognition-cdbme-2020 | 2020 | CDBME | CNN | BMSC tenogenic recognition proof-of-concept | CPU/GPU |
| cnn-tenogenic-differentiation-cmpb-2021 | 2021 | CMPB | 4 CNNs (incl. MobileNet) | >91% accuracy; Android deployment | Mobile (Android) |

---

## Papers by hardware target

| Hardware | Papers |
|---------|--------|
| CPU only | smart-stiffness-1d, intelligent-stiffness, physics-based-rnn, lstm-stiffness, lumbar-spine, rnn-cnn-shock-wave, rnn-plane-stress-damage |
| Intel Loihi | spiking-rnn-neuromorphic-cmame-2023, dissertation |
| Intel Loihi 2 | meta-learning-hybrid-spiking-npj-2026 |
| Innatera Xylo-Av2 | spiking-nn-viscoplastic-fem-cmame-2024, snn-engineering-mechanics-ewco-2024, dissertation |
| FPGA (PYNQ Z2 / Xilinx) | fpga-bnn-viscoplastic-mrc-2025 |
| Mobile (Android) | cnn-tenogenic-differentiation-cmpb-2021 |

---

## Papers by research phase

| Phase | Years | Theme | Papers |
|-------|-------|-------|--------|
| 1 — Smart stiffness | 2021–2022 | ANN/LSTM stiffness matrix replacement; Sobolev training | smart-stiffness-1d, intelligent-stiffness, lstm-stiffness-plate |
| 2 — Physics-based self-learning | 2022–2024 | RNN material integration; physics loss; REIIS | physics-based-rnn, lumbar-spine, rnn-cnn-shock-wave, rnn-plane-stress-damage |
| 3 — Neuromorphic / sustainable AI | 2023–2026 | SNN → neuromorphic chips; energy efficiency | spiking-rnn-neuromorphic, spiking-nn-viscoplastic, snn-engineering-mechanics, snn-nonlinear-regression, meta-learning-hybrid-spiking |
| 4a — FPGA + BNN | 2025 | Binary NN on FPGA; speed vs GPU | fpga-bnn-viscoplastic |
| 4b — CNN cell imaging | 2020–2024 | Phase-contrast microscopy; non-invasive classification | cnn-tenogenic-recognition, cnn-tenogenic-differentiation, cell-preserving-chondrocyte |
| Synthesis | 2024 | PhD dissertation unifying Phases 1–3 | dissertation-sustainable-brain-inspired |

---

## Key numeric claims

| Claim | Value | Paper slug |
|-------|-------|-----------|
| Speedup (ANN, 1D FEM) | 41% | smart-stiffness-1d-fem-mrc-2021 |
| Speedup (NN, truss/plate) | >90% | intelligent-stiffness-plate-beam-ijnme-2022 |
| Speedup (LSTM, plate) | 90.6% | lstm-stiffness-plate-pamm-2022 |
| Break-even (training amortised) | ~10 simulations | overview (Phases 1–2) |
| Speedup (LIF+RLIF SNN) | >30% | spiking-nn-viscoplastic-fem-cmame-2024 |
| Speedup (pseudo-explicit SNN) | >40% | snn-engineering-mechanics-ewco-2024 |
| Energy reduction (SNN vs CPU) | 1/1000 | snn-engineering-mechanics-ewco-2024 |
| Speedup (BNN on FPGA vs CPU) | 60% | fpga-bnn-viscoplastic-mrc-2025 |
| Speedup (BNN on FPGA vs RTX 4090) | 26% | fpga-bnn-viscoplastic-mrc-2025 |
| QAT training overhead (HSN + MAML) | +67% epochs; higher test loss | meta-learning-hybrid-spiking-npj-2026 |
| Regression accuracy (lumbar spine) | R²=0.988 | lumbar-spine-biomechanics-rnn-abme-2023 |
| Cell classification accuracy | >91% | cnn-tenogenic-differentiation-cmpb-2021 |
| Dissertation length | 240 pages | dissertation-sustainable-brain-inspired-2024 |
| PhD defended | 2024-09-12 | dissertation-sustainable-brain-inspired-2024 |

---

## Known contradictions / parameter variants

These are **not errors** — each row documents values that differ across papers and explains why.

| Claim | Paper A value | Paper B value | Resolution |
|-------|--------------|--------------|-----------|
| Steel Young's modulus E | 67 400 MPa (CMAME 2022, Table 1) | 198 600 MPa (MRC 2025, Table 1) | Different experimental steel specimens; both sourced from Stoffel (2005) but different publications (*Mech. Mater.* vs *ZAMM*). Never mix parameter sets between papers. |
| Steel kinematic hardening k | 110 MPa (CMAME 2022) | 167.88 MPa (MRC 2025) | Same campaign difference as E above. |
| Speedup metric basis | % of classical FEM wall time | % of classical FEM wall time | Same metric; values not comparable across BVPs of different size/complexity. |

See [[concepts/lemaitre-chaboche-parameters]] for the full parameter table and paper-level mapping.

---

## Concept definitions (one-liners)

| Concept page | Definition |
|-------------|-----------|
| [[concepts/neural-network-enhanced-fem]] | Replacing FEM subroutines (stiffness, material integration) with trained NNs for faster simulations |
| [[concepts/sobolev-training]] | Loss augmented with derivative term so NN learns force AND stiffness simultaneously |
| [[concepts/stiffness-matrix-replacement]] | Element tangent stiffness predicted by NN, eliminating Newton–Raphson iterations |
| [[concepts/physics-informed-neural-networks]] | NNs trained with physical equation residuals as loss terms (no labelled data needed) |
| [[concepts/self-learning-nn]] | NNs that update weights online during FEM deployment via physics loss — no new labelled data |
| [[concepts/recurrent-neural-networks-in-mechanics]] | LSTM, GRU, LMU as surrogates for history-dependent mechanical behaviour |
| [[concepts/spiking-neural-networks]] | Third-generation NNs communicating via sparse spikes; energy-efficient; deployable on neuromorphic chips |
| [[concepts/neuromorphic-computing]] | Brain-inspired chips (Loihi, Xylo-Av2) running SNNs at ~1/1000 CPU energy |
| [[concepts/sustainable-ai]] | Pursuit of AI methods (esp. SNNs) that reduce energy and memory consumption |
| [[concepts/viscoplasticity-modelling]] | Rate-dependent plastic deformation; Lemaitre–Chaboche constitutive law replaced by NN |
| [[concepts/attention-mechanism]] | Encoder-decoder attention for sequence-to-sequence structural dynamics; attention wins CM 2023 benchmark |
| [[concepts/meta-learning-maml]] | MAML meta-learning for physics-based self-learning NN initialisation; introduced in NPJ 2026 |
| [[concepts/binary-neural-networks]] | 1-bit weight/activation NNs; XNOR-popcount ops; FPGA-deployable; 60% faster than CPU |
| [[concepts/fpga-acceleration-nn]] | FPGAs as reconfigurable NN inference hardware; complement to neuromorphic ASICs |
| [[concepts/cnn-cell-imaging]] | CNN/YoloV8 for non-invasive stem cell and chondrocyte classification from phase-contrast images |
| [[concepts/nn-generation-replacement-levels]] | ANN/LSTM outputs K+F directly (element level); brain-inspired SNNs output σ+C at Gauss points; K+F assembled classically |

---

## Concept → source mapping

| Concept | Sources |
|---------|---------|
| neural-network-enhanced-fem | smart-stiffness-1d, intelligent-stiffness, physics-based-rnn, lstm-stiffness, spiking-rnn, spiking-nn-viscoplastic, snn-engineering-mechanics, rnn-plane-stress-damage, fpga-bnn, meta-learning-hybrid-spiking, dissertation |
| sobolev-training | smart-stiffness-1d, intelligent-stiffness, lstm-stiffness-plate-pamm |
| stiffness-matrix-replacement | smart-stiffness-1d, intelligent-stiffness, lstm-stiffness-plate-pamm |
| physics-informed-neural-networks | physics-based-rnn, spiking-nn-viscoplastic, snn-engineering-mechanics, rnn-plane-stress-damage |
| self-learning-nn | physics-based-rnn, spiking-nn-viscoplastic, snn-engineering-mechanics, rnn-plane-stress-damage, meta-learning-hybrid-spiking |
| recurrent-neural-networks-in-mechanics | physics-based-rnn, lstm-stiffness, lumbar-spine, rnn-cnn-shock-wave, rnn-plane-stress-damage |
| spiking-neural-networks | spiking-rnn-neuromorphic, spiking-nn-viscoplastic, snn-engineering-mechanics, rnn-plane-stress-damage, snn-nonlinear-regression, meta-learning-hybrid-spiking |
| neuromorphic-computing | spiking-rnn-neuromorphic, spiking-nn-viscoplastic, snn-engineering-mechanics, snn-nonlinear-regression, meta-learning-hybrid-spiking |
| viscoplasticity-modelling | physics-based-rnn, spiking-nn-viscoplastic, rnn-plane-stress-damage, fpga-bnn, meta-learning-hybrid-spiking |
| binary-neural-networks | fpga-bnn-viscoplastic-mrc-2025 |
| meta-learning-maml | meta-learning-hybrid-spiking-npj-2026 |
| fpga-acceleration-nn | fpga-bnn-viscoplastic-mrc-2025 |
| cnn-cell-imaging | cnn-tenogenic-recognition-cdbme-2020, cnn-tenogenic-differentiation-cmpb-2021, cell-preserving-chondrocyte-pamm-2024 |
| attention-mechanism | rnn-cnn-shock-wave-plates-cm-2023 |
| sustainable-ai | snn-engineering-mechanics-ewco-2024, spiking-rnn-neuromorphic, meta-learning-hybrid-spiking, dissertation |
| lemaitre-chaboche-parameters | physics-based-rnn-viscoplastic-cmame-2022, fpga-bnn-viscoplastic-mrc-2025, spiking-nn-viscoplastic-fem-cmame-2024, rnn-plane-stress-damage-mrc-2024, meta-learning-hybrid-spiking-npj-2026 |

---

## Methods / acronyms glossary

| Term | Full form | Definition | Paper(s) |
|------|-----------|-----------|---------|
| REIIS | Recurrent Explicit Implicit Integration Scheme | LSTM+FFNN replaces implicit return-mapping; self-updating physics loss | physics-based-rnn-cmame-2022 |
| LMU | Legendre Memory Unit | RNN cell with orthogonal delay buffer; good for long-range dependencies | spiking-rnn-cmame-2023, snn-engineering-mechanics, rnn-plane-stress-damage, snn-nonlinear-regression |
| LIF | Leaky Integrate-and-Fire | Standard spiking neuron model; fires when membrane potential crosses threshold | spiking-nn-viscoplastic-cmame-2024 |
| RLIF | Recurrent Leaky Integrate-and-Fire | LIF with recurrent connections; used as self-learning plastic corrector | spiking-nn-viscoplastic-cmame-2024 |
| HSN | Hybrid Spiking Neuron | Real-valued spiking neuron (not binary spike); combines ANN expressivity with SNN efficiency | meta-learning-hybrid-spiking-npj-2026 |
| MAML | Model-Agnostic Meta-Learning | Meta-learning algorithm; optimises initialisation for fast task-specific fine-tuning | meta-learning-hybrid-spiking-npj-2026 |
| BNN | Binary Neural Network | Weights and activations quantised to ±1; XNOR-popcount replaces multiply-accumulate | fpga-bnn-viscoplastic-mrc-2025 |
| QAT | Quantisation-Aware Training | Training with simulated quantisation to minimise accuracy loss at inference | meta-learning-hybrid-spiking-npj-2026 |
| Lemaitre–Chaboche | — | Classical viscoplastic constitutive law with isotropic/kinematic hardening; the material law replaced by NN in most papers | physics-based-rnn, spiking-nn-viscoplastic, fpga-bnn, meta-learning |
| Sobolev training | — | Loss = function error + weighted derivative error; trains NN on force AND stiffness simultaneously | smart-stiffness-1d, intelligent-stiffness, lstm-stiffness |
| Pseudo-explicit | — | Integration scheme where implicit corrector is approximated explicitly by SNN, reducing Newton–Raphson iterations | snn-engineering-mechanics-ewco-2024 |
| BMSC | Bone Marrow Stem Cell | Multipotent stem cells; classified by CNN into tenocyte/chondrocyte/BMSC in cell imaging thread | cnn-tenogenic papers |
| YoloV8 | — | Object detection/instance segmentation model; used for chondrocyte classification in PAMM 2024 | cell-preserving-chondrocyte-pamm-2024 |

---

## Collaborators quick-ref

| Name | Role | Papers co-authored in wiki |
|------|------|--------------------------|
| Marcus Stoffel | Principal supervisor / PI | All 17 papers |
| Bernd Markert | Co-author (earlier papers) | smart-stiffness-1d, intelligent-stiffness, physics-based-rnn, lstm-stiffness, lumbar-spine, rnn-cnn-shock-wave, spiking-rnn-neuromorphic |
| Franz Bamer | Co-author | physics-based-rnn-cmame-2022 |
| Nadja Blomeyer | Co-first author | lumbar-spine-biomechanics-rnn-abme-2023 |
| Rutwik Gulakala | Recurring co-author | multiple FEM, cell imaging, FPGA papers |
| Gözde Dursun | Lead author (cell imaging) | cnn-tenogenic-recognition-cdbme-2020, cnn-tenogenic-differentiation-cmpb-2021 |
| Vasileios Polydoras | Lead author | fpga-bnn-viscoplastic-mrc-2025 |
| Hyun Lee | Lead author | cell-preserving-chondrocyte-pamm-2024 |

_Entity pages exist for: [[Saurabh Balkrishna Tandale]], [[Marcus Stoffel]], [[Bernd Markert]], [[Franz Bamer]], [[Vasileios Polydoras]], [[Gözde Dursun]], [[Hyun Lee]]._

---

## Open research frontiers (as of 2026-04-07)

| Gap | Context |
|-----|---------|
| 3D solid elements | All FEM validations on plate/beam; 3D untested |
| Complex real geometries | Academic BVPs only; no real engineering structures |
| Other materials | Viscoplasticity dominates; hyperelasticity, damage-only, multi-physics not covered |
| Attention + FEM integration | CM 2023 attention enc-dec not yet embedded in FEM solver |
| MAML task distribution design | No principled method for choosing meta-tasks in FEM contexts |
| FPGA energy benchmarking | PYNQ Z2 lacks power monitoring; full energy comparison with neuromorphic deferred |
| Chondrocyte mechanoregulatory model | PAMM 2024 data is a first step; quantitative model is stated goal |

---

## Keyword → page index

| Keyword(s) | Go to |
|-----------|-------|
| stiffness, Newton-Raphson, tangent | [[concepts/stiffness-matrix-replacement]], [[concepts/neural-network-enhanced-fem]], [[concepts/nn-generation-replacement-levels]] |
| brain-inspired vs ANN, replacement level, Gauss point vs element | [[concepts/nn-generation-replacement-levels]] |
| Sobolev, derivative loss, force+stiffness | [[concepts/sobolev-training]] |
| implicit integration, return-mapping, REIIS | [[concepts/physics-informed-neural-networks]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]] |
| self-learning, online update, no labels | [[concepts/self-learning-nn]] |
| LSTM, GRU, TCN, RNN | [[concepts/recurrent-neural-networks-in-mechanics]] |
| spike, spiking, LMU, LIF, RLIF, SNN | [[concepts/spiking-neural-networks]] |
| Loihi, Xylo, neuromorphic, brain-inspired | [[concepts/neuromorphic-computing]] |
| energy, sustainable, 1/1000 | [[concepts/sustainable-ai]], [[sources/snn-engineering-mechanics-ewco-2024]] |
| viscoplastic, Lemaitre, Chaboche, plasticity | [[concepts/viscoplasticity-modelling]], [[concepts/lemaitre-chaboche-parameters]] |
| attention, encoder-decoder, shock wave | [[concepts/attention-mechanism]], [[sources/rnn-cnn-shock-wave-plates-cm-2023]] |
| MAML, meta-learning, HSN, hybrid spiking | [[concepts/meta-learning-maml]], [[sources/meta-learning-hybrid-spiking-npj-2026]] |
| BNN, binary, FPGA, XNOR, PYNQ | [[concepts/binary-neural-networks]], [[concepts/fpga-acceleration-nn]], [[sources/fpga-bnn-viscoplastic-mrc-2025]] |
| CNN, stem cell, BMSC, tenocyte, Android | [[concepts/cnn-cell-imaging]], [[sources/cnn-tenogenic-differentiation-cmpb-2021]] |
| chondrocyte, YoloV8, bioreactor, dedifferentiation | [[concepts/cnn-cell-imaging]], [[sources/cell-preserving-chondrocyte-pamm-2024]] |
| lumbar spine, biomechanics, cyclic loading | [[sources/lumbar-spine-biomechanics-rnn-abme-2023]] |
| dissertation, thesis, RWTH, unified framework | [[sources/dissertation-sustainable-brain-inspired-2024]] |
| Tandale, Stoffel, RWTH, IAM, PostDoc | [[Saurabh Balkrishna Tandale]] |
| phase 1, early work, 2021–2022 | overview Phase 1; smart-stiffness-1d, intelligent-stiffness, lstm-stiffness |
| phase 3, neuromorphic, 2023–2026 | overview Phase 3; spiking-rnn, spiking-nn-viscoplastic, snn-engineering-mechanics |

---

## See also
[[Overview]], [[index]]
