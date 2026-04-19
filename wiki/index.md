# KnowledgeHub Index
_Last updated: 2026-04-19 — 48 pages total_

## Overview
- [[Overview]] — evolving synthesis of all knowledge
- [[Quick Reference]] — flat facts table: speedups, models, hardware, concept→source map (primary grep target)

## Sources (17)
| Page | Summary | Date | Tags |
|------|---------|------|------|
| [[sources/smart-stiffness-1d-fem-mrc-2021]] | ANN stiffness replacement for 1D FEM (truss/beam); Sobolev training; 41% speedup | 2021 | FEM, ANN, Sobolev |
| [[sources/intelligent-stiffness-plate-beam-ijnme-2022]] | NN stiffness replacement for truss/beam/plate; >90% speedup; constitutive + stiffness replacement | 2022 | FEM, ANN, RNN, plate |
| [[sources/physics-based-rnn-viscoplastic-cmame-2022]] | REIIS: LSTM+FFNN physics-informed implicit integration for Lemaitre–Chaboche viscoplasticity | 2022 | PINN, LSTM, viscoplasticity |
| [[sources/lstm-stiffness-plate-pamm-2022]] | LSTM stiffness replacement for plate structures; 90.6% speedup; Hyperband tuning | 2022 | LSTM, plate, Sobolev |
| [[sources/lumbar-spine-biomechanics-rnn-abme-2023]] | LSTM RNN predicts lumbar spine (L4L5) moment-RoM under cyclic loading; R²=0.988 | 2023 | biomechanics, LSTM, spine |
| [[sources/rnn-cnn-shock-wave-plates-cm-2023]] | Benchmark: LSTM vs GRU vs TCN vs attention encoder-decoder for shock wave plates; attention wins | 2023 | RNN, attention, shock-wave |
| [[sources/spiking-rnn-neuromorphic-cmame-2023]] | First SNN for solid mechanics BVPs; spiking LMU + autoencoding; deployed on Loihi | 2023 | SNN, neuromorphic, Loihi |
| [[sources/spiking-nn-viscoplastic-fem-cmame-2024]] | Physics-based self-learning SNN (LIF+RLIF) in FEM implicit integration; >30% speedup; Xylo-Av2 | 2024 | SNN, self-learning, Xylo |
| [[sources/snn-engineering-mechanics-ewco-2024]] | Pseudo-explicit SNN (spiking LMU) in FEM; >40% speedup; energy 1/1000 on Xylo-Av2 | 2024 | SNN, pseudo-explicit, sustainable |
| [[sources/rnn-plane-stress-damage-mrc-2024]] | LMU self-learning solver for plane stress viscoplasticity + isotropic damage in explicit FEM | 2024 | LMU, self-learning, damage |
| [[sources/snn-nonlinear-regression-neuromorphic-npj]] | General SNN regression framework for transient signals; spiking LMU; neuromorphic processors | 2024 | SNN, regression, neuromorphic |
| [[sources/cell-preserving-chondrocyte-pamm-2024]] | YoloV8 classifies intact/dedifferentiated chondrocytes in bioreactor without sacrificing cells | 2024 | mechanobiology, YoloV8, chondrocyte |
| [[sources/dissertation-sustainable-brain-inspired-2024]] | PhD dissertation (240 pp) unifying ANN→SNN FEM framework; RWTH Aachen, Sept 2024 | 2024 | dissertation, FEM, SNN, self-learning |
| [[sources/fpga-bnn-viscoplastic-mrc-2025]] | BNN replaces Lemaitre–Chaboche law in FEM; FPGA 60% faster than CPU, 26% faster than RTX 4090 | 2025 | FPGA, BNN, viscoplasticity |
| [[sources/meta-learning-hybrid-spiking-npj-2026]] | Hybrid Spiking Neurons (HSN) + MAML meta-learning for physics-based FEM solvers; Loihi 2 | 2026 | MAML, HSN, meta-learning |
| [[sources/cnn-tenogenic-recognition-cdbme-2020]] | Early CNN proof-of-concept for BMSC tenogenic differentiation recognition (conference paper) | 2020 | CNN, stem-cell, BMSC |
| [[sources/cnn-tenogenic-differentiation-cmpb-2021]] | 4 CNN architectures classify BMSCs/tenocytes/chondrocytes; >91% accuracy; Android deployment | 2021 | CNN, stem-cell, tenogenesis, smartphone |

## Entities (9)
| Page | Type | Summary |
|------|------|---------|
| [[Saurabh Balkrishna Tandale]] | person | PostDoc at RWTH Aachen; NN-enhanced FEM, SNN, neuromorphic computing; 17 publications in wiki |
| [[Marcus Stoffel]] | person | Principal supervisor / PI; co-author all 17 papers; source of Lemaitre–Chaboche parameter sets |
| [[Bernd Markert]] | person | Co-author on Phase 1–2 papers (2021–2023); RWTH Aachen mechanics professor |
| [[Franz Bamer]] | person | Co-author on CMAME 2022 physics-based RNN paper |
| [[Vasileios Polydoras]] | person | Lead author on FPGA BNN MRC 2025; responsible for hardware benchmarking |
| [[Gözde Dursun]] | person | Lead author on cell-imaging CNN papers (CDBME 2020, CMPB 2021) |
| [[Hyun Lee]] | person | Lead author on chondrocyte YoloV8 paper (PAMM 2024) |
| [[Nadja Blomeyer]] | person | Co-first author on lumbar spine biomechanics RNN paper (ABME 2023) |
| [[Rutwik Gulakala]] | person | Recurring co-author across FEM, cell imaging, and FPGA papers |

## Concepts (18)
| Page | Summary |
|------|---------|
| [[concepts/neural-network-enhanced-fem]] | Replacing FEM subroutines (stiffness, material integration) with trained NNs for faster simulations |
| [[concepts/sobolev-training]] | Loss function augmented with derivative term so NN learns both force and stiffness simultaneously |
| [[concepts/stiffness-matrix-replacement]] | Element tangent stiffness predicted by NN, eliminating Newton–Raphson iterations |
| [[concepts/physics-informed-neural-networks]] | NNs trained with physical equation residuals as loss terms |
| [[concepts/self-learning-nn]] | NNs that update weights online during FEM deployment using physics loss, without labelled data |
| [[concepts/recurrent-neural-networks-in-mechanics]] | LSTM, GRU, LMU as surrogates for history-dependent mechanical behaviour |
| [[concepts/spiking-neural-networks]] | Third-generation NNs communicating via sparse spikes; energy-efficient; deployable on neuromorphic chips |
| [[concepts/neuromorphic-computing]] | Brain-inspired chips (Loihi, Xylo-Av2) that run SNNs at orders-of-magnitude lower energy |
| [[concepts/sustainable-ai]] | Pursuit of AI methods (esp. SNNs) that reduce energy and memory consumption |
| [[concepts/viscoplasticity-modelling]] | Classical and NN-based approaches to rate-dependent plastic deformation (Lemaitre–Chaboche) |
| [[concepts/attention-mechanism]] | Encoder-decoder attention for sequence-to-sequence structural dynamics prediction |
| [[concepts/meta-learning-maml]] | MAML meta-learning for physics-based self-learning NN initialisation in FEM |
| [[concepts/binary-neural-networks]] | 1-bit weight/activation NNs; XNOR-popcount ops; FPGA-deployable; 60% faster than CPU |
| [[concepts/fpga-acceleration-nn]] | FPGAs as reconfigurable hardware for NN inference; complement to neuromorphic ASICs |
| [[concepts/cnn-cell-imaging]] | CNN/YoloV8 for non-invasive stem cell and chondrocyte classification from phase-contrast images |
| [[concepts/lemaitre-chaboche-parameters]] | Numerical parameter sets for the Lemaitre–Chaboche model; two distinct steel campaigns + copper; full comparison table |
| [[concepts/nn-generation-replacement-levels]] | ANN/LSTM replaces K+F directly (element level); brain-inspired SNNs replace constitutive law (Gauss-point level) — two distinct strategies |
| [[concepts/solver-strategies-at-gaussian-points]] | Evolution from classical iterative solvers (Newton–Raphson, Pegasus) to self-learning and MAML-based NN solvers |
