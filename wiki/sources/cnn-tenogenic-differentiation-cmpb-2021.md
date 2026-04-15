---
title: "Source: CNN for Tenogenic Differentiation Recognition (CMPB 2021)"
type: source
tags: [CNN, cell-imaging, stem-cell, tenogenic-differentiation, biomedicine, image-classification, BMSC, TensorFlow-Lite]
created: 2026-04-07
updated: 2026-04-07
sources: 1
---
**CNN-based non-invasive, label-free recognition of bone marrow stem cell differentiation into tenocytes from phase-contrast microscopy images; best model achieves >91% accuracy and is deployed on Android smartphone.**

## Key points
- Journal: *Computer Methods and Programs in Biomedicine* 208 (2021) 106279
- Authors: Gözde Dursun, Saurabh Balkrishna Tandale, Rutwik Gulakala, Jörg Eschweiler, Mersedeh Tohidnezhad, Bernd Markert, Marcus Stoffel — RWTH Aachen University
- Problem: traditional stem cell characterisation (antibody staining, gene expression) is invasive, destroys cellular material, and is costly — CNN replaces this with label-free morphology analysis
- Three cell classes: chondrocytes (negative control), BMSCs (control), tenocytes (differentiated, positive)
- Tenogenic differentiation induced by BMP-12 growth factor at 10 ng/ml and 50 ng/ml for 12, 24, 48 h

## Methodology / approach
**Image preprocessing pipeline** (visible in Fig. 2):
1. Import phase-contrast image → convert to grayscale
2. Apply Sobel edge detection (highlights single-cell boundaries)
3. Apply Gaussian filter (reduces Sobel noise)
4. Augmentation: flipping, rotation, zoom, brightness shifts

**Four CNN architectures trained from scratch** (Fig. 4):
| Model | Inspired by | Trainable params |
|-------|-------------|-----------------|
| Model 1 | VGG-16 | 1.231 × 10⁶ |
| Model 2 | Inception | 0.154 × 10⁶ |
| Model 3 | ResNet | 2.029 × 10⁶ |
| Model 4 | Inception-ResNet V2 | 0.590 × 10⁶ |

All use ReLU activation in conv layers, Softmax at output, categorical cross-entropy loss, global max-pooling, dropout regularisation, batch normalisation.

**Smartphone deployment:** Model converted to TensorFlow Lite and deployed on Android via a custom app (Fig. 12–13). The app loads an image, runs inference via TFLite interpreter, and displays class probabilities.

## Key claims & evidence
- **Best model: Model 4 (Inception-ResNet V2) with IE + DA** → training accuracy 0.946, testing accuracy 0.904 (Table 1)
- Pre-trained fine-tuned models (VGG-16, Inception V3, ResNet50, IRV2) all overfit severely (training ~0.99, testing ~0.33–0.41); from-scratch training far superior with small dataset
- Image enhancement (IE = Sobel + Gaussian) consistently reduces chondrocyte misclassification across all models
- Data augmentation (DA) improves generalisation more than IE alone
- Combined IE + DA: Model 4 achieves best balance of accuracy and training time (389 epochs, 2381 s)
- Validation dataset II (generalisation to unseen groups): Model 1 and Model 4 achieve 100% on all three groups at 50 ng/ml
- Key finding: 50 ng/ml BMP-12 for 12 h and 24 h causes the most significant scleraxis expression (P < 0.001), confirming differentiation — CNN results agree with immunofluorescence staining

## Visual content
- **Fig. 1**: Phase-contrast microscopy images of chondrocytes, BMSCs, and tenocytes — visually distinct morphologies (chondrocytes = cobblestone-round; BMSCs = spindle-like; tenocytes = elongated)
- **Fig. 2**: Preprocessing flowchart — grayscale → Sobel → Gaussian → stored image
- **Fig. 3**: Standard CNN architecture (conv → max pool → dense)
- **Fig. 4**: Four custom CNN architectures with layer configurations
- **Fig. 7**: Immunofluorescence scleraxis staining results (statistical significance bars)
- **Fig. 9**: Training/validation accuracy and loss curves for all 4 models
- **Fig. 10**: Average chondrocyte misclassification percentage — IE reduces false positives
- **Fig. 11**: Feature map analysis — first and last layer activations; edges progressively abstracted
- **Fig. 12**: App UML activity diagram
- **Fig. 13**: Android app screenshots showing classification results

## Limitations / caveats
- Dataset is small (number of images not stated precisely, but acknowledged as a limitation)
- Models trained from scratch — transfer learning from pre-trained models failed due to small dataset
- Binary classes at inference time (BMSC or tenocyte); chondrocytes serve only as a control, not a clinical target
- Smartphone app: inference speed and power consumption not benchmarked

## Contradictions with existing wiki
- No contradictions. This paper introduces a completely new application domain (cell biology / regenerative medicine) not previously in the wiki.

## Quotes worth keeping
> "An ideal platform for characterizing the stem cell differentiation would be based on a label-free, non-invasive method which has high reproducibility, requires a small amount of sample, and low costs."

> "Model 4 proposed based on Inception-ResNet V2 achieved the highest accuracy and the least training time for cell classification."

## See also
[[Saurabh Balkrishna Tandale]], [[concepts/cnn-cell-imaging]], [[sources/cnn-tenogenic-recognition-cdbme-2020]], [[sources/cell-preserving-chondrocyte-pamm-2024]]
