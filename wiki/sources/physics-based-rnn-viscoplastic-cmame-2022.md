---
title: "Source: Physics-Based Self-Learning Recurrent Neural Network enhanced time integration scheme for computing viscoplastic structural finite element response"
type: source
tags: [PINN, RNN, LSTM, viscoplasticity, Lemaitre-Chaboche, self-learning, FEM, REIIS, implicit-integration]
created: 2026-04-06
updated: 2026-04-07
sources: 1
---
**Introduces REIIS — a physics-informed LSTM+FFNN implicit integration scheme for viscoplastic FEM that replaces the classical return-mapping algorithm and self-learns via constitutive law residuals.**

## Key points
- **REIIS** = Recurrent Enhanced Implicit Integration Scheme.
- Belongs to the class of Physics-Informed Neural Networks (PINNs), specifically applied to material integration in FEM.
- A combination of LSTM (to capture history/path dependence) and FFNN (for accurate state variable representation) is used.
- Physical constraints: the constitutive relations of the **Lemaitre–Chaboche viscoplastic model** are incorporated as additional loss terms.
- Co-authors: Tandale, Bamer, Markert, Stoffel.
- Validated on Reissner–Mindlin plate elements at different strain rates.

## Lemaitre–Chaboche constitutive equations

$$\dot{\varepsilon}^p = \tfrac{3}{2}\,\dot{\bar{\varepsilon}}^p \frac{\sigma' - X}{J_2(\sigma' - X)}$$
$$\dot{\bar{\varepsilon}}^p = \left\langle \frac{J_2(\sigma' - X) - k}{K} \right\rangle^n$$
$$\dot{X} = \tfrac{2}{3}\,a\,\dot{\varepsilon}^p - s\,X\,\dot{\bar{\varepsilon}}^p$$

State variables solved: $[\sigma', X, \bar{\varepsilon}^p, \dot{\varepsilon}^p]^T$.

## Material parameters (Table 1) — source: Stoffel (2005) *Mech. Mater.* 37(12)

| Parameter | Steel | Copper |
|-----------|-------|--------|
| E (MPa) | 67 400 | 113 066 |
| ν | 0.32 | 0.32 |
| n | 2.79 | 8.15 |
| a (MPa) | 3 100 | 98 939.30 |
| s | 110 | 1 533.41 |
| K (MPa·s^1/n) | 3.42 | 11.45 |
| k (MPa) | 110 | 180.0 |

Steel pretrained; Copper used for online adaptive learning demonstration only.

## NN architecture (Table 2)

| Component | Value |
|-----------|-------|
| Architecture | Sequential LSTM |
| LSTM layers | 2 |
| Hidden layers | 2 |
| Units per LSTM | 128 |
| Dense output layers | 1 |
| Output activation | Sigmoid |
| Optimizer | Adam, lr=0.001 |
| Batch sizes (curriculum) | [2, 4, 8, 16, 32, 64, 128] |
| Pre-training sequences | 400 training / 100 test |

**Input:** $[\check{\sigma}', \check{X}, k, K]$ per timestep — **Output:** $\Delta\dot{\bar{\varepsilon}}^p$

## FEM setup (Table 4)

| BVP | Element | Geometry (mm²) | Thickness (mm) | E (MPa) | k (MPa) |
|-----|---------|---------------|---------------|---------|---------|
| Single element (Fig. 10) | 9-noded Reissner–Mindlin plate | 30 × 20 | 1 | 67 400 | 110 |
| Plate / plate+hole (Figs. 11, 12) | 9-noded Reissner–Mindlin plate | 125 × 125 | 1 | 67 400 | 110 |

- 9 × 5 = 45 integration points per element (normal + shear)
- Training strain rate range: $[10^{-3}, 10^{-7}]$ s
- Max equivalent stress: 138.89 MPa; max equivalent plastic strain: 0.01081

## Quantitative results (Table 5) — MAE (%)

| BVP | σ₁₁ | σ₂₂ | σ₂₃ | σ₁₃ | σ₁₂ | ε̄ᵖ | X_global |
|-----|-----|-----|-----|-----|-----|-----|---------|
| Fig. 10 (single, known pattern) | 0.00198 | 0.019 | 0.02023 | 0.0394 | 0.03316 | 0.00524 | 0.003223 |
| Fig. 9 (single, new pattern) | 0.034 | 0.0121 | 0.45 | 0.556 | 0.19 | 0.0432 | 0.08423 |
| Fig. 11 (plate, no hole) | 0.0012 | 0.0754 | 0.00112 | 0.0004785 | 0.00984 | 0.00112 | 0.0001789 |
| Fig. 12 (plate, with hole) | 0.00458 | 0.00067 | 0.00458 | 0.002189 | 0.0004884 | 0.0045 | 0.00059654 |

## Speed

- Single element (known pattern): ~**29% faster** than classical FEM
- Multi-element plate BVPs: ~**40% faster** than classical FEM
- Note: implemented in Python; further gains expected with parallel FEM solver

## Online learning rate scheduler (Table 3)

| Optimizer | Batch size | Initial LR | Decay | Decay steps |
|-----------|-----------|-----------|-------|-------------|
| Adam | 32 | 1e-2 | 0.96 | 5000 |

## Methodology / approach
1. Pretrained offline with combined data-driven (λ₁=λ₂=1) + physics-based loss (yield function residual).
2. Deployed in plastic corrector step of implicit Euler integration (Algorithm 2).
3. Online self-learning triggered when physics residual exceeds tolerance (λ₁=0, λ₂=1 — physics only).
4. LSTM internal state [S₁, S₂] tracks per-Gauss-point path history independently.

## Limitations / caveats
- Focused on Lemaitre–Chaboche; generalisation requires adapting physics loss.
- Speed gain for unseen loading patterns is worse (NN must do many online steps).
- Python implementation — timing is conjectural.

## Quotes worth keeping
> "We observe that obeying physical constraints leads to improved robustness in the learning of REIIS and broadens the scope of integrating the NNs in FEM."

## See also
[[concepts/physics-informed-neural-networks]], [[concepts/self-learning-nn]], [[concepts/recurrent-neural-networks-in-mechanics]], [[concepts/viscoplasticity-modelling]], [[concepts/lemaitre-chaboche-parameters]], [[sources/spiking-nn-viscoplastic-fem-cmame-2024]], [[Saurabh Balkrishna Tandale]]
