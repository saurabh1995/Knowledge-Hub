---
title: "Attention Mechanism"
type: concept
tags: [attention, encoder-decoder, transformer, sequence-to-sequence, structural-dynamics]
created: 2026-04-06
updated: 2026-04-19
---
**A neural network component that allows a model to selectively focus on the most relevant parts of an input sequence when producing each element of an output sequence.**

## Core idea
In sequence-to-sequence (encoder–decoder) architectures:
- The **encoder** processes the full input sequence and produces hidden states.
- The **decoder** generates outputs step by step, using an **attention score** to weight the encoder hidden states — paying more attention to the most relevant input timestep for each output timestep.

This enables the model to handle long input sequences without compressing all information into a fixed-size vector.

## Modified attention in CM 2023
In Tandale's CM 2023 paper (shock wave-loaded plates), a **GRU encoder-decoder with a modified attention mechanism** was proposed and compared against LSTM, GRU, and TCN baselines:
- The attention mechanism showed it intuitively assigns physical meaning: the decoder pays maximum attention to the **pressure information at the current time step** when predicting displacement — analogous to how FEM computes displacement sequentially from applied loads.
- The attention-based architecture outperformed all other tested models on both training and validation experiments (including extrapolation beyond the training range).

## Physical interpretation
The attention weights provide a degree of explainability:
- High attention on current pressure input → the model's decision is dominated by the instantaneous loading.
- The sequential nature mirrors the FEM computation itself.

## Context in Tandale's work
The CM 2023 paper is primarily a benchmark/comparison study. The attention encoder-decoder is the best architecture but is not yet embedded in an FEM solver. Future work (mentioned in the paper) envisions combining it with the intelligent stiffness computation framework.

## Mathematical formulation

Additive attention (Bahdanau-style) as adapted in [[sources/rnn-cnn-shock-wave-plates-cm-2023]] (Section 3.2):

Alignment score between encoder hidden state $\mathbf{h}_i$ and previous decoder state $\mathbf{s}_{t-1}$:

$$e_{ti} = \mathbf{v}^\top \tanh\!\left(\mathbf{W}_h\,\mathbf{h}_i + \mathbf{W}_s\,\mathbf{s}_{t-1}\right)$$

Attention weights (softmax over all encoder steps $T_x$):

$$\alpha_{ti} = \frac{\exp(e_{ti})}{\displaystyle\sum_{j=1}^{T_x} \exp(e_{tj})}$$

Context vector fed to the decoder at step t:

$$\mathbf{c}_t = \sum_{i=1}^{T_x} \alpha_{ti}\,\mathbf{h}_i$$

Decoder hidden state update (GRU cell):

$$\mathbf{s}_t = f\!\left(\mathbf{s}_{t-1},\,\mathbf{y}_{t-1},\,\mathbf{c}_t\right)$$

$\mathbf{W}_h$, $\mathbf{W}_s$, $\mathbf{v}$ are learned parameters; $\mathbf{y}_{t-1}$ is the previous decoder output. In the CM 2023 structural dynamics application, $\alpha_{ti}$ peaks when $t = i$, reflecting that displacement at each step is primarily driven by the instantaneous pressure load.

## See also
[[concepts/recurrent-neural-networks-in-mechanics]], [[sources/rnn-cnn-shock-wave-plates-cm-2023]]
