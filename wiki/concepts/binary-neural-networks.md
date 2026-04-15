---
title: "Binary Neural Networks"
type: concept
tags: [BNN, quantization, hardware-acceleration, FPGA, energy-efficiency, surrogate-model]
created: 2026-04-07
updated: 2026-04-07
---
**Neural networks where weights and activations are constrained to 1-bit values (−1 or +1), replacing floating-point multiply-accumulate operations with hardware-efficient XNOR-popcount operations, enabling deployment on FPGAs and low-power devices.**

## Core idea
Standard deep neural networks use 32-bit floating-point weights. BNNs quantize weights and activations to a single bit:
- +1 for set bit, −1 for unset bit
- Floating-point multiply-accumulate (MAC) → bitwise XNOR followed by popcount (bit counting)
- This is dramatically cheaper in terms of memory, compute, and power on programmable hardware

## Architecture
BNNs share the same layer structure as dense FFNNs but binary layers differ:
1. **1-bit values**: all inputs, weights, and output activations in binary layers
2. **Batch normalisation** applied before activation (learned scale γ and shift β)
3. **Sign activation function**: sign(x) = −1 if x < 0, else +1
4. **XNOR-popcount**: matrix-vector multiplication in the binary domain

For regression tasks (non-binary outputs), a **hybrid architecture** is needed:
- Real-valued encoder (dense layers with ReLU) → converts continuous inputs to binary representation
- Binary core (1–2 binary layers) → runs on FPGA
- Real-valued decoder (dense layers with ReLU) → converts binary back to continuous outputs

## FPGA deployment
BNNs are uniquely suited to FPGAs because binary operations map directly to LUT (Look-Up Table) and FF (Flip-Flop) resources:
- **FINN library** (Xilinx): compiles BNN to hardware
  1. Network preparation: streamlines batch normalisation (collapses affine transforms), converts ops to multiply-add
  2. Hardware build: each binary layer → Matrix-Vector-Threshold Unit (MVTU) IP block, stitched via Vitis HLS + Vivado
- **MVTU**: the core computational unit — processes one BNN layer via distributed PEs (hardware neurons) and SIMD lanes (hardware synapses)

## Performance in FEM context (MRC 2025)
| Hardware | Relative BNN forward-pass time |
|----------|-------------------------------|
| PYNQ Z2 FPGA | 1× (baseline, fastest) |
| Intel i7-13700K CPU | 2.5× slower |
| NVIDIA RTX 4090 GPU | 1.35× slower |

## Relationship to other quantised NNs
- **Quantised Neural Networks (QNNs)**: reduced precision (2, 4, 8-bit) — BNNs are the extreme case (1-bit)
- **SNNs**: also sparse/energy-efficient but communicate via temporal spike events, not binary weights; SNNs require neuromorphic ASICs (Loihi, Xylo), whereas BNNs can run on general-purpose FPGAs

## Limitations
- Pure BNNs cannot solve regression problems (real-valued outputs) without the hybrid encoder-decoder workaround
- Accuracy loss vs. full-precision networks, especially for complex nonlinear regression
- FPGA toolchain (FINN, Vitis HLS, Vivado) is Xilinx-specific; portability requires rework
- Energy measurement not yet possible on PYNQ Z2 (no on-chip power monitoring)

## See also
[[concepts/fpga-acceleration-nn]], [[concepts/neuromorphic-computing]], [[concepts/neural-network-enhanced-fem]], [[concepts/viscoplasticity-modelling]], [[sources/fpga-bnn-viscoplastic-mrc-2025]]
