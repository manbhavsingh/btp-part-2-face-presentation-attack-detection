# BTP Part 2 — Face Presentation Attack Detection

A comparative face presentation attack detection (PAD) benchmark built around the OULU-NPU dataset. The notebook implements a Wang et al. (CVPR 2020) spatial-gradient baseline and compares it with an enhanced Central Difference Convolution (CDC) model with multi-task depth supervision.

> **Notebook status:** The original Colab notebook is not uploaded yet. The repository currently contains the project documentation and setup files. Dataset files, Google Drive caches, and trained checkpoints are not included.

## Research objectives

- Implement a single-frame spatial-gradient baseline with Residual Spatial Gradient Blocks (RSGB).
- Implement an enhanced CDC-based model with multi-scale feature fusion and classification/depth output heads.
- Compare fixed Wang-style multi-task loss weighting with homoscedastic uncertainty-based loss weighting.
- Evaluate using subject-disjoint Train, Development, and Test partitions.
- Calibrate the decision threshold on the Development split and report PAD metrics on the held-out Test split.

## Models

### Wang et al. (CVPR 2020) baseline
The notebook's baseline uses residual spatial-gradient blocks with fixed Sobel gradient operators, a depth prediction branch, and a classification head. Its fixed multi-task objective is:

\[
\mathcal{L}=0.8\mathcal{L}_{CE}+0.2(\mathcal{L}_{EDL}+0.5\mathcal{L}_{CDL})
\]

### Proposed BTP v17 model
The enhanced model introduces learnable Central Difference Convolutions, multi-scale feature-pyramid fusion, dual output heads, and Kendall–Gal-style homoscedastic uncertainty weighting for the classification and depth tasks. The notebook also retains raw perimeter context rather than applying outer-region inpainting.

## Recorded benchmark

The saved notebook output reports the following held-out test results:

| Model | Dev ACER | Test APCER | Test BPCER | Test ACER | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Wang et al. (CVPR 2020) SGTD | 9.34% | 16.21% | 4.60% | 10.40% | 0.9605 |
| Proposed BTP v17 SGTD | 6.71% | 9.79% | 5.75% | 7.77% | 0.9798 |

The notebook reports a **2.64 percentage-point absolute reduction in Test ACER** for the proposed model compared with the baseline.

The recorded held-out test partition contains 414 samples: 87 bona fide and 327 presentation attacks. The results above are transcribed from the notebook's saved outputs; rerunning the notebook may produce different results depending on the available data, caches, checkpoints, and runtime.

## Dataset and split

The notebook parses OULU-NPU image filenames using five fields: phone ID, session ID, subject ID, presentation-attack type ID, and recording ID. It maps type ID 1 to bona fide/live and IDs 2–5 to presentation attacks.

The saved run reports 1,701 samples and subject-disjoint partitions of:

- Train: 1,029 samples
- Development: 258 samples
- Test: 414 samples

**Dataset access:** OULU-NPU is not distributed in this repository. Obtain it through the dataset's official access process and place it in Google Drive in a location recognized by the notebook.

## Running the notebook

The notebook is designed for **Google Colab** and expects Google Drive access. A GPU runtime (the saved run used a Tesla T4) is recommended.

1. Open the notebook in Google Colab.
2. Obtain the OULU-NPU dataset separately and make it available in your Google Drive.
3. If reusing precomputed aligned crops, PRNet depth targets, or trained weights, place those files in the Drive cache locations expected by the notebook.
4. Run the notebook cells in order.

### Important reproducibility note

The notebook is cache-aware and attempts to reuse existing Drive assets. The original dataset, cached face crops, PRNet depth maps, and model checkpoints are **not** part of this repository. In particular, when a bona fide depth cache entry is missing, the notebook contains a procedural fallback depth target; this is not a substitute for the original PRNet-derived target. For a faithful reproduction of the recorded benchmark, provide the corresponding original caches and checkpoints and verify the execution summary.

## Metrics

- **APCER** — Attack Presentation Classification Error Rate.
- **BPCER** — Bona Fide Presentation Classification Error Rate.
- **ACER** — Average Classification Error Rate, conventionally the mean of APCER and BPCER.
- **ROC-AUC** — Area under the Receiver Operating Characteristic curve.

## Repository structure

```text
.
├── README.md
├── notebooks/
│   └── BTP_MASTER_v17_0_SOTA_CDCN_PRNET_OPTIMAL.ipynb
├── figures/
│   └── .gitkeep
├── results/
│   └── .gitkeep
├── requirements.txt
└── .gitignore
```

## Dependencies

See [requirements.txt](requirements.txt). The notebook also imports `google.colab` and uses Google Drive APIs supplied by the Colab environment.

## Notes

- No OULU-NPU dataset files, Google Drive contents, credentials, or trained model checkpoints are included.
- Please check the terms and access conditions of OULU-NPU and the cited research before redistributing data or derived assets.
- This is an academic research project, not a production-ready biometric security system.
