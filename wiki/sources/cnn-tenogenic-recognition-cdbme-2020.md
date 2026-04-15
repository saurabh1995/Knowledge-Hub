---
title: "Source: CNN for Tenogenic Differentiation — Conference Paper (CDBME 2020)"
type: source
tags: [CNN, stem-cell, tenogenic-differentiation, biomedicine, BMSC, conference]
created: 2026-04-07
updated: 2026-04-07
sources: 1
---
**Short (5-page) conference paper presenting early CNN results for non-invasive BMSC tenogenic differentiation recognition — predecessor to the full CMPB 2021 journal paper.**

## Key points
- Venue: *Current Directions in Biomedical Engineering* (CDBME) 2020; DOI: 10.1515/cdbme-2020-3051
- Authors: Gözde Dursun, Saurabh Balkrishna Tandale, Jörg Eschweiler, Mersedeh Tohidnezhad, Bernd Markert, Marcus Stoffel — RWTH Aachen
- Same research question as CMPB 2021: classify BMSCs vs. tenocytes from phase-contrast microscopy images using CNN
- BMP-12/GDF-7 growth factor used to induce tenogenic differentiation (10 ng/ml and 50 ng/ml, 12/24/48 h)

## Methodology / approach
- BMSCs at passage 3, density ~3500 cells/cm²
- Immunofluorescence staining with tenomodulin (Tnmd) antibody as biomarker (CMPB 2021 uses scleraxis/SCX instead)
- Phase-contrast images for CNN training; characterised phenotype by immunostaining
- CNN approach for label-free, non-invasive classification — early/preliminary architecture (not the 4-model comparison of 2021)

## Key claims & evidence
- CNN is able to recognise the differentiated phenotype of BMSCs based on cell morphology alone
- Demonstrates proof-of-concept for label-free characterisation replacing complex immunostaining procedures
- Preliminary results; full quantitative comparison deferred to journal version

## Limitations / caveats
- Conference paper: limited detail on CNN architecture, training parameters, and accuracy numbers compared to CMPB 2021
- Uses tenomodulin (Tnmd) as biomarker vs. scleraxis (SCX) in the 2021 paper — possible difference in differentiation specificity
- Smaller scope: no smartphone deployment, no image enhancement comparison, no data augmentation study

## Contradictions with existing wiki
- None. Confirmed by the 2021 journal paper which supersedes this work.

## See also
[[sources/cnn-tenogenic-differentiation-cmpb-2021]], [[Saurabh Balkrishna Tandale]], [[concepts/cnn-cell-imaging]]
