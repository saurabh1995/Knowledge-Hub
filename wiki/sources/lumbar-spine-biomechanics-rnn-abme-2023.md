---
title: "Source: Prediction of Temperature and Loading History Dependent Lumbar Spine Biomechanics Under Cyclic Loading Using Recurrent Neural Networks"
type: source
tags: [biomechanics, spine, LSTM, RNN, cyclic-loading, viscoelasticity, L4L5, ABME]
created: 2026-04-06
updated: 2026-04-06
sources: 1
---
**Trains an LSTM RNN to predict changing moment–range-of-motion curves of L4L5 spinal segments under extended cyclic loading, incorporating testing time and temperature as inputs; R² = 0.988.**

## Key points
- Application of RNNs to **experimental biomechanics** — departure from the FEM-centric papers.
- Co-first authors: **Nadja Blomeyer** and Saurabh Balkrishna Tandale.
- 6 human lumbar spinal segments (L4L5) tested in vitro.
- Loading protocol: cyclic pure moments (±7.5 Nm) for 18 hours total per specimen, across three directions: flexion-extension (FE), axial rotation (AR), lateral bending (LB).
- LSTM RNN trained to predict moment–Range of Motion (RoM) curves including creep and viscoelastic effects.
- Inputs to RNN: total testing time + testing temperature.

## Methodology / approach
- Experimental: six specimens loaded cyclically for up to 18 h, unloaded for recovery periods.
- Data preprocessing: curves parameterised; symmetry of AR and LB exploited to expand dataset (179 training sequences from 6 specimens).
- LSTM trained via backpropagation; internal hidden state implicitly captures path-dependent viscoelastic behaviour.
- Validated on unseen loading sequences.

## Key claims & evidence
- **R² = 0.988** for mean prediction accuracy on validation sequences.
- Strong positive correlation between total testing time and the ratio of 3rd-to-last loading cycle (rload).
- Including temperature and testing time as inputs improves prediction quality.
- RNN advantages over classical material modelling: (1) lower computational cost, (2) specimen-specific inputs, (3) no need for manual material parameter calibration.
- RNN can predict patient-specific spinal behaviour for unseen loading conditions.

## Limitations / caveats
- Small sample size (n=6 specimens, all advanced donor age).
- Data limited to L4L5 segment; other spinal levels require new training.
- In vitro testing does not fully replicate in vivo conditions.
- Only 179 training sequences — limited diversity in training data.

## Quotes worth keeping
> "Neither time- and cost-expensive in vitro tests nor complex and computationally expensive in silico studies have to be performed [once the RNN is trained]."

## See also
[[concepts/recurrent-neural-networks-in-mechanics]], [[concepts/viscoplasticity-modelling]], [[Saurabh Balkrishna Tandale]]
