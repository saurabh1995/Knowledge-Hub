---
title: "Source: Cell-Preserving Scheme for Chondrocyte Dedifferentiation Research (PAMM 2024)"
type: source
tags: [mechanobiology, chondrocyte, YoloV8, instance-segmentation, DNN, dedifferentiation, bioreactor, cartilage]
created: 2026-04-07
updated: 2026-04-07
sources: 1
---
**YoloV8 instance segmentation model classifies intact vs. dedifferentiated chondrocytes from phase-contrast images without sacrificing cells, enabling continuous mechanobiological study under tensile bioreactor loading.**

## Key points
- Journal: *Proceedings in Applied Mathematics and Mechanics* (PAMM) 2024; DOI: 10.1002/pamm.202400172
- Authors: Hyun Lee, Nikolai Panitschewski, Heiko Topol, Saurabh Tandale, Marcus Stoffel — RWTH Aachen + New Uzbekistan University
- Problem: understanding chondrocyte (CH) dedifferentiation under mechanical loading is critical for osteoarthritis (OA) treatment and autologous CH implantation (ACI), but traditional immunoassays require fixing/killing cells, preventing longitudinal study of the same population
- Solution: DNN detects CHs phenotypically from phase-contrast images → cells remain alive and can be mechanically loaded further

## Methodology / approach
**Three-class instance segmentation** (Fig. 2, 6):
| Class label | Meaning |
|-------------|---------|
| CH | Intact chondrocyte (cobblestone-shaped) |
| e_CH | Early dedifferentiation (deformed shape, early) |
| l_CH | Late dedifferentiation (fibroblast-like elongated) |

**Model**: YoloV8 — one-stage object detection with CSP (Cross Stage Partial) backbone; selected over two-stage Mask R-CNN for faster training and hyperparameter tuning
- Dataset: ~80 images for training, 10 validation, 10 test; manually labelled via Labelme annotation tool
- Training: phase-contrast microscopy images (Leica DFC450 C camera); YoloV8 converged with CIoU box loss
- Loss curves (Fig. 5): box_loss = CIoU loss; shown for training and validation

**Tensile bioreactor experiment** (Fig. 4):
- Custom in-house tensile bioreactor
- CHs cultured on PDMS (poly(dimethylsiloxane)) membrane, cells face downward immersed in media
- Glass cover with filter-capped air holes; tensile stretch applied to PDMS
- Two CH sources: healthy specimen (passage 4, mostly cobblestone) and OA patient (passage 1 = mostly cobblestone; passage 4 = mostly fibroblast-like elongated)

**Workflow** (Fig. 2 — key figure):
1. Label → train DNN on phase-contrast images with unloaded (intact) cells
2. Apply mechanical loading in bioreactor
3. Acquire phase-contrast images → DNN detects and counts intact/dedifferentiated CHs → compare I vs. II

## Key claims & evidence
- Trained YoloV8 successfully distinguishes CHs by phenotype without fixing cells (Fig. 6)
- Tensile experiment result (Fig. 7 — bar chart):
  - Before loading: intact CH = 61.8%, early dediff (e_CH) = 24.5%, late dediff (l_CH) = ~13.7%
  - After loading: intact CH = 56.8%, early dediff (e_CH) = 18.4%, late dediff (l_CH) = ~24.8%
  - Mechanical loading shifts the CH population toward late dedifferentiation — captured non-invasively
- Analysis takes minutes (vs. days for traditional immunoassays)
- Model trained on 80 images achieves satisfactory one-stage detection results

## Visual content
- **Fig. 1**: ACI procedure diagram showing how dedifferentiation occurs during in vitro culture expansion
- **Fig. 2**: Proposed scheme: unloaded cells (I) → DNN training → loaded cells (II) → DNN inference → compare — cells survive throughout
- **Fig. 3**: Phase-contrast images of (A) healthy CHs passage 4 (cobblestone), (B) OA passage 1, (C) OA passage 4 (fibroblast-like elongated)
- **Fig. 4**: Tensile bioreactor setup — PDMS membrane, cell side down, two side views
- **Fig. 5**: YoloV8 learning curves (CIoU box_loss) for training and validation
- **Fig. 6**: DNN predictions with bounding boxes + class labels (CH / e_CH / l_CH) and confidence probabilities on phase-contrast images and bioreactor experiment image
- **Fig. 7**: Bar chart — cell count distribution before vs. after loading; shows increase in l_CH fraction

## Limitations / caveats
- Small dataset (80 training images); model may not generalise to images from different microscopes or cell lines
- YoloV8 is a general-purpose object detector, not customised for biological cells — dedicated architectures may perform better
- Only one loading condition tested; dose-response relationship for mechanobiological modelling not yet established
- Confounding factor: dedifferentiation also occurs from passage number alone (OA passage 4 ≈ elongated even without loading)

## Contradictions with existing wiki
- No contradictions. Introduces mechanobiology as a new research theme connecting mechanical stimuli to cell biology — adjacent to but distinct from the FEM-focused core of the wiki.

## Quotes worth keeping
> "Replacing the traditional immunoassays with a DNN, which can distinguish intact and dedifferentiated CHs from phase-contrast microscope images, will allow us to get the solution faster while preserving the cells."

## See also
[[Saurabh Balkrishna Tandale]], [[concepts/cnn-cell-imaging]], [[sources/cnn-tenogenic-differentiation-cmpb-2021]], [[sources/lumbar-spine-biomechanics-rnn-abme-2023]]
