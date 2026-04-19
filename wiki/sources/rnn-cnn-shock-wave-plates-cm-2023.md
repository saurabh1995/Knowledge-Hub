---
title: "Source: Recurrent and convolutional neural networks in structural dynamics: a modified attention steered encoder–decoder architecture versus LSTM versus GRU versus TCN"
type: source
tags: [RNN, LSTM, GRU, TCN, attention, encoder-decoder, shock-wave, structural-dynamics, surrogate, CM]
created: 2026-04-06
updated: 2026-04-19
sources: 1
---
**Benchmarks LSTM, GRU, TCN, and a modified attention encoder–decoder for predicting nonlinear dynamic response of shock wave-loaded steel/aluminium/copper plates; attention encoder-decoder wins.**

## Key points
- Comparative study of four NN architectures for predicting plate deformation in shock tube experiments.
- Materials: steel, aluminium, copper plates with geometric and physical (viscoplastic) nonlinearities.
- Training data: sequences of pressure input → displacement output across a wide range of strain rates.
- Validation includes data **beyond** the training range (extrapolation).
- Best architecture: **GRU encoder-decoder with modified attention mechanism**.

## Methodology / approach
Four architectures proposed and compared:
1. **Multi-layer LSTM** — baseline recurrent model.
2. **Multi-layer GRU** — simpler gated recurrent model.
3. **Multi-layer TCN** — dilated causal convolutions; parallelisable.
4. **Modified attention encoder-decoder** — GRU encoder; GRU decoder with attention scores over encoder hidden states.

- Attention weights interpreted physically: decoder pays maximum attention to pressure at the *current* time step — mirrors FEM's sequential displacement computation.
- Simple **scaling strategy** introduced to predict beyond the training input range.

## Key claims & evidence
- GRU encoder-decoder with attention achieves best accuracy on both training and validation experiments.
- **Model parameter counts (Table 3)**: Attention enc-dec = **231,810** params; enc-dec (no attn) = 232,961; TCN = 232,961; GRU = 274,273; LSTM = 309,281. Attention model is the most parameter-efficient among recurrent models.
- **RMSE benchmark (Fig. 14)**: Attention enc-dec = **0.03553** (lowest); TCN = 0.0793; GRU = 0.421455; LSTM = 0.60136. ~17× lower RMSE than vanilla LSTM.
- Attention weights provide physical interpretability (pressure dominates displacement prediction at each time step).
- Scaling strategy enables extrapolation beyond training range when learned patterns are similar.
- All models trained on experimentally measured pressure-displacement sequences. Loss function: MSE.

## Limitations / caveats
- Surrogate is geometry-specific and material-specific — retraining needed for new plate types.
- Model not yet embedded in FEM solver (stand-alone surrogate for experimental prediction).
- Extrapolation only works when underlying patterns match training patterns.

## Quotes worth keeping
> "The attention weights also intuitively assign a physical resemblance to the decoder which pays maximum attention to the pressure information at the current time step."

## See also
[[concepts/attention-mechanism]], [[concepts/recurrent-neural-networks-in-mechanics]], [[concepts/neural-network-enhanced-fem]], [[Saurabh Balkrishna Tandale]]
