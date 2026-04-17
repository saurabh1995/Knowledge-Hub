# KnowledgeHub Log
_Append-only. Never edit or delete past entries._
_Grep tip: `grep "^## \[" wiki/log.md | tail -10`_

---

## [2026-04-06] manual | KnowledgeHub initialised

- Created directory structure: `raw/`, `raw/assets/`, `wiki/`, `wiki/sources/`, `wiki/entities/`, `wiki/concepts/`
- Created `CLAUDE.md` schema
- Created `wiki/index.md` (master catalog)
- Created `wiki/log.md` (this file)
- Created `wiki/overview.md` (stub)
- Pages created: [[Overview]]
- Wiki is empty and ready for first ingest

---

## [2026-04-06] ingest | Batch ingest of 12 papers by Saurabh Balkrishna Tandale (2021–2026)

- Ingested 12 PDFs from `raw/Papers/` spanning MRC, IJNME, CMAME, PAMM, ABME, CM, EWCO, NPJ Unconventional Computing
- Papers cover three research phases: (1) smart stiffness / Sobolev training, (2) physics-based self-learning RNNs, (3) neuromorphic SNN-enhanced FEM
- Pages created: [[sources/smart-stiffness-1d-fem-mrc-2021]], [[sources/intelligent-stiffness-plate-beam-ijnme-2022]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[sources/lstm-stiffness-plate-pamm-2022]], [[sources/lumbar-spine-biomechanics-rnn-abme-2023]], [[sources/rnn-cnn-shock-wave-plates-cm-2023]], [[sources/spiking-rnn-neuromorphic-cmame-2023]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/rnn-plane-stress-damage-mrc-2024]], [[sources/snn-nonlinear-regression-neuromorphic-npj]], [[sources/meta-learning-hybrid-spiking-npj-2026]]
- Entity created: [[Saurabh Balkrishna Tandale]]
- Concepts created: [[concepts/neural-network-enhanced-fem]], [[concepts/sobolev-training]], [[concepts/stiffness-matrix-replacement]], [[concepts/physics-informed-neural-networks]], [[concepts/self-learning-nn]], [[concepts/recurrent-neural-networks-in-mechanics]], [[concepts/spiking-neural-networks]], [[concepts/neuromorphic-computing]], [[concepts/sustainable-ai]], [[concepts/viscoplasticity-modelling]], [[concepts/attention-mechanism]], [[concepts/meta-learning-maml]]
- Pages updated: [[Overview]], [[index]]
- Total pages: 26

---

## [2026-04-07] ingest | Batch ingest of 5 new documents (dissertation + bio/FPGA papers)

- Ingested 5 documents from `raw/Papers/`: PhD dissertation (240 pp), 2 cell-imaging papers (CMPB 2021, CDBME 2020), 1 FPGA/BNN paper (MRC 2025), 1 mechanobiology paper (PAMM 2024)
- Text extraction + visual page rendering (PyMuPDF) used for all PDFs; figures analysed via vision
- Two new research threads added to wiki: (A) FPGA + Binary Neural Networks, (B) CNN for cell imaging / mechanobiology
- Pages created: [[sources/dissertation-sustainable-brain-inspired-2024]], [[sources/cnn-tenogenic-differentiation-cmpb-2021]], [[sources/cnn-tenogenic-recognition-cdbme-2020]], [[sources/fpga-bnn-viscoplastic-mrc-2025]], [[sources/cell-preserving-chondrocyte-pamm-2024]]
- Concepts created: [[concepts/binary-neural-networks]], [[concepts/fpga-acceleration-nn]], [[concepts/cnn-cell-imaging]]
- Pages updated: [[Saurabh Balkrishna Tandale]], [[concepts/neural-network-enhanced-fem]], [[concepts/neuromorphic-computing]], [[Overview]], [[index]]
- Total pages: 40 (was 26)

---

## [2026-04-07] manual | Retrieval speed optimisation

- Updated `CLAUDE.md`: grep-first added to QUERY workflow (step 2), LINT workflow (step 1), and Behavioural rules
- Updated `CLAUDE.md`: INGEST workflow now includes step 9 to update quick-ref.md
- Updated `CLAUDE.md`: session start now greps log.md headers instead of reading full file
- Updated `CLAUDE.md`: directory layout documents quick-ref.md
- Pages created: [[Quick Reference]]
- Pages updated: [[index]]
- Total pages: 41

---

## [2026-04-07] manual | Further retrieval speed optimisation

- QUERY workflow: removed mandatory index.md read; quick-ref.md is now the sole first-stop for queries
- QUERY workflow: parallel grep across sources/concepts/entities added (step 2)
- Behavioural rules: updated to reflect quick-ref-first; added explicit parallel grep rule; removed conflicting "always read index.md" rule
- Session start: index.md replaced with quick-ref.md for query sessions; index.md deferred to ingest/lint only
- Pages updated: [[CLAUDE.md schema]]

---

## [2026-04-09] query | Copper material properties for viscoplastic model

- Query: material properties used for copper in viscoplastic material model simulations
- Answered from: [[sources/physics-based-rnn-viscoplastic-cmame-2022]] (Table 1, Stoffel 2005 *Mech. Mater.* 37(12))
- Key facts: E = 113 066 MPa, ν = 0.32, n = 8.15, a = 98 939.30 MPa, s = 1 533.41, K = 11.45 MPa·s^1/n, k = 180.0 MPa; Lemaitre–Chaboche model; copper used for online adaptive learning only (steel used for pretraining)
- Other viscoplastic sources (spiking-nn-viscoplastic-fem-cmame-2024, fpga-bnn-viscoplastic-mrc-2025) use steel only

---

## [2026-04-07] query | Xylo-Av2 deployment procedure

- Query: how to deploy SNNs on the Xylo-Av2 chip from SynSense
- Source detail extracted from: `raw/Papers/Tandale_2024_EWCO.pdf` (Section 6.4) and `raw/Papers/Tandale_2024_CMAME.pdf` (Section 7.4)
- Key facts: toolchain = Rockpool (init) + Samna (deploy); chip supports LIF/RLIF only; max 16 inputs / 8 outputs; binary spike input; no on-chip training; spiking layers 111× vs GPU, 3161× vs CPU
- Pages updated: [[concepts/neuromorphic-computing]] (added full Xylo-Av2 deployment section), [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/snn-engineering-mechanics-ewco-2024]]

---

## [2026-04-10] lint | Retrieval hardening and hallucination prevention pass

### Issues fixed
- **Deleted** `entities/saurabh-balkrishna-tandale.md` (empty file outside `wiki/`; all wikilinks resolved to it instead of the populated page)
- **Fixed** entity wikilink format in [[sources/fpga-bnn-viscoplastic-mrc-2025]] and [[sources/physics-based-rnn-viscoplastic-cmame-2022]]: `[[entities/saurabh-balkrishna-tandale]]` → `[[Saurabh Balkrishna Tandale]]`
- **Created** [[concepts/lemaitre-chaboche-parameters]] — was referenced in 3 source pages but did not exist; now contains full parameter comparison table for Steel A, Steel B, and Copper
- **Corrected** `wiki/index.md` page count: 41 → 44

### Enhancements
- **Created** 6 collaborator entity pages: [[Marcus Stoffel]], [[Bernd Markert]], [[Franz Bamer]], [[Vasileios Polydoras]], [[Gözde Dursun]], [[Hyun Lee]]
- **Added** `## Known contradictions / parameter variants` section to `wiki/quick-ref.md` (two steel parameter sets explained)
- **Added** `lemaitre-chaboche-parameters` row to concept→source mapping in quick-ref
- **Updated** keyword→page index in quick-ref: viscoplastic keywords now also point to [[concepts/lemaitre-chaboche-parameters]]
- **Updated** collaborators quick-ref note with entity page links

### CLAUDE.md schema hardening
- INGEST workflow: added step 10 — wikilink validation before closing ingest
- Source page structure: added rule that quantitative claims must cite PDF section/table/page number
- QUERY workflow step 4: added explicit guard — only cite pages indexed in `wiki/index.md`

- Pages created: [[concepts/lemaitre-chaboche-parameters]], [[Marcus Stoffel]], [[Bernd Markert]], [[Franz Bamer]], [[Vasileios Polydoras]], [[Gözde Dursun]], [[Hyun Lee]]
- Pages updated: [[sources/fpga-bnn-viscoplastic-mrc-2025]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[wiki/index.md]], [[wiki/quick-ref.md]]

---

## [2026-04-10] query | Brain-inspired networks and stiffness/force computation

- Query: are brain-inspired (spiking) networks used to compute the stiffness matrix and internal force vector?
- Answer synthesised from: [[concepts/stiffness-matrix-replacement]], [[concepts/neural-network-enhanced-fem]], [[concepts/sobolev-training]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]]
- Key finding: No — direct K+F replacement is Phase 1 (2nd-gen ANN/LSTM); brain-inspired SNNs replace the constitutive law at Gauss-point level; K and F are assembled classically from SNN-predicted stress and tangent
- Pages created: [[concepts/nn-generation-replacement-levels]]
- Pages updated: [[wiki/index.md]], [[wiki/quick-ref.md]]

## [2026-04-10] query | FPGA BNN speedup vs CPU and RTX 4090

- Query: "What speedup does the FPGA binary neural network paper achieve compared to CPU, and compared to the RTX 4090?"
- Sources consulted: [[wiki/quick-ref.md]] (fully answered; lines 29, 73–74), [[sources/fpga-bnn-viscoplastic-mrc-2025]]
- Answer: 60% faster than CPU; 26% faster than RTX 4090 (both from fpga-bnn-viscoplastic-mrc-2025)
- Answer filed as new page: no

## [2026-04-10] query | What is Sobolev training and which papers use it?

- Query: definition of Sobolev training and wiki papers that use it
- Answered from: [[concepts/sobolev-training]] (primary), [[wiki/quick-ref.md]] (concept→source mapping)
- Papers using Sobolev training: [[sources/smart-stiffness-1d-fem-mrc-2021]] (MRC 2021), [[sources/intelligent-stiffness-plate-beam-ijnme-2022]] (IJNME 2022), [[sources/lstm-stiffness-plate-pamm-2022]] (PAMM 2022)
- Answer filed as new page: no (concept page [[concepts/sobolev-training]] already covers this fully)

## [2026-04-10] query | Neuromorphic chips vs FPGAs for FEM neural surrogates — energy and speed

- Query: "Comparing neuromorphic chips and FPGAs for running neural surrogates in FEM — which is better for energy, and which for speed?"
- Sources consulted: [[sources/fpga-bnn-viscoplastic-mrc-2025]], [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[concepts/neuromorphic-computing]], [[concepts/fpga-acceleration-nn]], [[concepts/binary-neural-networks]]
- Key finding: Neuromorphic (Xylo-Av2) wins on energy — spiking layers use 8 nJ vs 892 nJ GPU (111×) and 25,289 nJ CPU (3,161×); ~1/1000 energy vs CPU. FPGA wins on raw inference speed — 1.34 ms BNN forward pass vs 3.41 ms CPU (60% faster) and 1.83 ms RTX 4090 (26% faster). FPGA energy unquantified (PYNQ Z2 hardware limitation). No direct side-by-side on same BVP exists.
- Answer filed as new page: no (pending Saurabh approval)
- gap identified: no direct FPGA vs neuromorphic comparison on same BVP; FPGA energy benchmarking deferred

## [2026-04-12] query | What is an FPGA board?

- Query: "What do you mean by FPGA board?"
- Sources consulted: [[concepts/fpga-acceleration-nn]], [[sources/fpga-bnn-viscoplastic-mrc-2025]], wiki/quick-ref.md
- Key finding: FPGA = Field Programmable Gate Array, reconfigurable hardware; specific board in wiki is PYNQ Z2 (Xilinx); used to run BNN for viscoplastic FEM; 60% faster than CPU, 26% faster than RTX 4090
- Answer filed as new page: no

---

## [2026-04-13] query | Solver strategies in NPJ meta-learning article

- Query: "What type of solvers have I mentioned in the article with meta-learning in npj?"
- Sources consulted: `raw/Papers/Tandale_&_Stoffel_NPJ_UNCONV_2026.pdf` (full PDF)
- Key findings:
  - **Classical solvers:** Newton-Raphson, Pegasus method, false position method (all root-finding / iterative methods used at Gaussian points)
  - **Integration scheme:** Backward Euler (implicit integration; generates physics-based loss via residual)
  - **Self-learning approach:** Physics-based NN solver replacing iterative loops; minimal/no online training for deformation modes seen in pretraining
  - **Meta-learning (MAML):** Second-order gradient pretraining; MAML model learns feature reuse not rapid learning; only final dense layer requires adaptation during inner loop (Fig. 4)
  - **Bounded softplus activation:** Final layer activation ensuring solution (Δεₚ) lies in physically admissible interval; combines root-finding (guarantee) + gradient descent advantages
  - **Iteration reduction:** 35% fewer iterations at Gaussian points (BVP1: 5056 vs 7845 Pegasus); 20% fewer (BVP2: 13258 vs 16593)
- Pages created: [[concepts/solver-strategies-at-gaussian-points]]
- Pages updated: [[wiki/index.md]], [[wiki/log.md]]

## [2026-04-16] query | Speedup comparison — EWCO 2024, CMAME 2024, self-learning CMAME 2022

- Query: speedup results from EWCO 2024, CMAME 2024, and physics-based self-learning CMAME 2022
- Sources consulted: [[sources/snn-engineering-mechanics-ewco-2024]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]]
- Key findings:
  - CMAME 2022 (REIIS): ~29% single element, ~40% multi-element plate BVP vs classical FEM
  - CMAME 2024 (LIF+RLIF SNN): 24.02% (BVP1), 30.98% (BVP2), 17.54% (combined nonlinearity) vs classical FEM; 111× energy vs GPU, 3,161× vs CPU on Xylo-Av2
  - EWCO 2024 (Spiking LMU, pseudo-explicit): >40% vs classical FEM; same energy figures as CMAME 2024
  - Cross-paper note: EWCO > CMAME 2024 in speedup, but BVPs differ — not directly comparable
  - Gap: CMAME 2022 and CMAME 2024 per-BVP figures not yet in quick-ref.md

## [2026-04-16] query | MAML speedup at Gaussian points

- Query: "Speed up obtained through MAML at Gaussian points"
- Sources consulted: [[concepts/solver-strategies-at-gaussian-points]], [[sources/meta-learning-hybrid-spiking-npj-2026]]
- Key findings:
  - Iteration reduction: 35% fewer (BVP1: 7,845 → 5,056) and 20% fewer (BVP2: 16,593 → 13,258) vs. Pegasus solver (Table 3, NPJ 2026)
  - Wall-clock speedup: 19% for BVP1, 7.3% for BVP2 (gap due to NN forward pass cost > single classical iteration)
  - MAML enables generalisability (5 training sequences vs. ~30 for transfer learning); iteration reduction is a property of MAML-HSNN in inference mode
  - Note: 19% / 7.3% wall-clock figures present in concept page without explicit paper section; recommend verifying against raw PDF if citing in manuscript

## [2026-04-15] query | Deepened wiki for proposal/article writing

- Added `## Mathematical formulation` sections to 8 concept pages (LIF/RLIF/LMU/HSN equations, Lemaitre–Chaboche constitutive equations, MAML inner/outer loop + QAT, XNOR-popcount, LSTM gates + LMU state-space, FEM residual + NN replacement, REIIS physics loss)
- Deepened 3 thin source pages with architecture tables, exact speedup numbers, accuracy tables, and energy data extracted from raw PDFs: [[sources/spiking-rnn-neuromorphic-cmame-2023]], [[sources/meta-learning-hybrid-spiking-npj-2026]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]]
- Updated CLAUDE.md schema to include `## Mathematical formulation` as a standard optional concept page section
- Updated CLAUDE.md QUERY workflow to check `## Mathematical formulation` sections for writing-support queries

---

## [2026-04-17] query | Quantization impact on graded spiking network learning ability (NPJ 2026)

- Query: how quantization affects learning ability of HSN + MAML model from NPJ 2026
- Initial wiki answer was incomplete: quick-ref.md mentioned QAT but lacked quantization performance metrics
- Raw source verification: extracted detailed QAT impact from `raw/Papers/Tandale_&_Stoffel_NPJ_UNCONV_2026.pdf` using pdftotext
- Key findings verified from raw source:
  - **Bit-widths**: 8-bit weights, 16-bit integer spiking outputs/membrane potential (not uniform 32-bit as wiki summary stated)
  - **Performance degradation**: QAT HSN + MAML requires 100 outer-loop epochs vs. 60 for non-quantized (~67% increase); higher test loss (Table 1)
  - **Mechanism**: Meta/second-order gradients amplify biased approximations from Straight-Through Estimators (STE) for quantization + surrogate gradients for spikes; meta-update drives parameters to regions appearing good under bias but worse under true quantized dynamics
  - **Hardware tradeoff**: QAT ensures knowledge retention on Loihi 2 deployment; only final dense layer requires CPU/GPU adaptation during inference
- Pages updated:
  - [[sources/meta-learning-hybrid-spiking-npj-2026]]: expanded Table 1 with full RMSE values, added detailed QAT performance impact section, added QAT penalty to limitations
  - [[concepts/meta-learning-maml]]: updated application section and QAT subsection with empirical performance data and mechanism explanation
  - [[wiki/quick-ref.md]]: added QAT training overhead metric (+67% epochs)
- Answer filed as new wiki page: no (integrated into existing source + concept pages)
