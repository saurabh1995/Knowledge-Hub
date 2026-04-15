---
title: "Lemaitre-Chaboche Parameters"
type: concept
tags: [viscoplasticity, Lemaitre-Chaboche, material-parameters, steel, copper, FEM]
created: 2026-04-10
updated: 2026-04-10
---
**Numerical parameter sets for the Lemaitre–Chaboche viscoplastic constitutive model as used across Tandale's FEM papers — two distinct steel campaigns and one copper set.**

## Constitutive equations

$$\dot{\varepsilon}^p = \tfrac{3}{2}\,\dot{\bar{\varepsilon}}^p \frac{\sigma' - X}{J_2(\sigma' - X)}$$
$$\dot{\bar{\varepsilon}}^p = \left\langle \frac{J_2(\sigma' - X) - k}{K} \right\rangle^n$$
$$\dot{X} = \tfrac{2}{3}\,a\,\dot{\varepsilon}^p - s\,X\,\dot{\bar{\varepsilon}}^p$$

State variables: $[\sigma', X, \bar{\varepsilon}^p, \dot{\varepsilon}^p]^T$ — deviatoric stress, back-stress, accumulated plastic strain, plastic strain rate.

## Parameter sets used in the wiki

Both steel sets ultimately derive from Stoffel (2005) but represent **different experimental campaigns** — they are not interchangeable.

| Parameter | Steel A (CMAME 2022) | Steel B (MRC 2025) | Copper (CMAME 2022) |
|-----------|---------------------|-------------------|---------------------|
| E (MPa) | 67 400 | 198 600 | 113 066 |
| ν | 0.32 | — | 0.32 |
| k (MPa) | 110 | 167.88 | 180.0 |
| a (MPa) | 3 100 | 2 500 | 98 939.30 |
| s | 110 | 20.30 | 1 533.41 |
| K (MPa·s^1/n) | 3.42 | 63.12 | 11.45 |
| n | 2.79 | 4.22 | 8.15 |
| ρ (kg/m³) | — | 7 806 | — |

**Primary sources:**
- Steel A + Copper: Stoffel (2005) *Mech. Mater.* 37(12) — used in CMAME 2022 (Table 1)
- Steel B: Stoffel (2005) *ZAMM* 85(9) — used in MRC 2025 (Table 1)

## Which papers use which set

| Paper | Material | Set |
|-------|----------|-----|
| [[sources/physics-based-rnn-viscoplastic-cmame-2022]] | Steel (pretraining) + Copper (online adapt.) | Steel A + Copper |
| [[sources/spiking-nn-viscoplastic-fem-cmame-2024]] | Steel | Steel A (CMAME lineage) |
| [[sources/rnn-plane-stress-damage-mrc-2024]] | Steel | Steel A (CMAME lineage) |
| [[sources/fpga-bnn-viscoplastic-mrc-2025]] | Steel | Steel B |
| [[sources/meta-learning-hybrid-spiking-npj-2026]] | Steel | Steel A (CMAME lineage) |

## Critical note for queries

When a query asks for "the steel parameters" — always check which paper is being discussed. E=67 400 MPa (Steel A) and E=198 600 MPa (Steel B) both appear in the wiki and are **not contradictions** — they are physically different steel specimens from different Stoffel experiments.

## See also

[[concepts/viscoplasticity-modelling]], [[sources/physics-based-rnn-viscoplastic-cmame-2022]], [[sources/fpga-bnn-viscoplastic-mrc-2025]]
