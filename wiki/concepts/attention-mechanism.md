---
title: "Attention Mechanism"
type: concept
tags: [attention, encoder-decoder, transformer, sequence-to-sequence, structural-dynamics]
created: 2026-04-06
updated: 2026-04-06
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

## See also
[[concepts/recurrent-neural-networks-in-mechanics]], [[sources/rnn-cnn-shock-wave-plates-cm-2023]]
