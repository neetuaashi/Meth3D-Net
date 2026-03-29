# Meth3D-Net V6

**Integrative Multi-Scale Epigenomic Framework for Methylation Prediction, Chromatin Instability Detection, and lncRNA–miRNA Regulatory Network Identification in Medulloblastoma**

*Neetu Singh — King George's Medical University (KGMC), Lucknow, India*
*neetusingh@kgmcindia.edu*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python 3.10](https://img.shields.io/badge/Python-3.10-blue.svg)](https://python.org)
[![Kaggle](https://img.shields.io/badge/Run%20on-Kaggle-20BEFF.svg)](https://kaggle.com)

---

## Overview

Meth3D-Net V6 is a multi-input deep learning framework that integrates:

- **Local CpG methylation context** from whole-genome bisulfite sequencing (WGBS)
- **Genomic positional features** — GC content, CpG island membership, distance to TSS
- **Hi-C-derived 3D chromatin structure** — proxy features from neighbourhood aggregation

Applied first to Roadmap Epigenomics WGBS reference data (H1 ESC and IMR90, GSE19418) and then validated across 763 primary medulloblastoma samples (GSE85212), the framework delivers:

| Capability | Key result |
|------------|-----------|
| Genome-wide methylation prediction | Mean Pearson r = 0.871 across autosomes |
| Hi-C proxy coverage | 100% (all chromosomes; V5 had 0%) |
| Chromatin instability hotspot | HLA locus, chr6:25–35 Mb, CT z = 4.8 |
| Epigenetic ageing | 42 tumours with accelerated DNAmAge (5.5%) |
| lncRNA regulatory network | TARID (7.3), DINO (5.8), H19 (5.2), MALAT1 (5.1) |
| Pseudo-clinical risk model | XGBoost AUC = 0.6551; RF CV AUC = 0.6466 ± 0.033 |

---

## Repository Structure

```
Meth3D-Net/
│
├── README.md                         ← This file
├── LICENSE                           ← MIT
├── .gitignore
├── requirements.txt                  ← Full Python environment
│
├── notebooks/
│   ├── 01_meth3dnet_v6_genome_training.ipynb     → Figures S1–S3
│   ├── 02_methylation_pipeline_hdac.ipynb         → Figures S4–S9
│   ├── 03_lncrna_geo_extraction.ipynb             → Figure S10
│   └── 04_pseudo_clinical_risk_model.ipynb        → Figures S11–S15
│
├── docs/
│   ├── NOTEBOOK_GUIDE.md             ← How to run each notebook
│   ├── DATA_ACCESS.md                ← Where to download all datasets
│   └── RESULTS_SUMMARY.md           ← Key verified numerical results
│
└── figures/
    ├── S1_genome_prediction.png
    ├── S2_HLA_locus.png
    ├── S3_instability_landscape.png
    ├── S4_DNAmAge_distribution.png
    ├── S5_cancer_epigenetic_signature.png
    ├── S6_multiaxis_landscape.png
    ├── S7_DNA_damage.png
    ├── S8_OG_TSG_heatmap.png
    ├── S9_OG_TSG_volcano.png
    ├── S10_lncRNA_prioritisation.png
    ├── S11_RF_feature_importance.png
    ├── S12_ROC_curves.png
    ├── S13_SHAP_summary.png
    ├── S14_KaplanMeier.png
    └── S15_Cox_forest_plot.png
```

---

## Quick Start

```bash
git clone https://github.com/neetuaashi/Meth3D-Net.git
cd Meth3D-Net
pip install -r requirements.txt
```

Download the required public datasets (see [`docs/DATA_ACCESS.md`](docs/DATA_ACCESS.md)), then run notebooks in order 01 → 02 → 03 → 04.

Each notebook contains a **PATH CONFIGURATION block in Cell 1** — set `LOCAL_BASE` to your data directory. All other paths are derived automatically.

---

## Datasets

All data are publicly available. Raw files are not hosted here due to size.

| Dataset | Accession | Used in |
|---------|-----------|---------|
| H1 ESC WGBS (Roadmap Epigenomics) | GEO: GSE19418 | Notebook 01 |
| IMR90 Fibroblast WGBS (Roadmap Epigenomics) | GEO: GSE19418 | Notebook 01 |
| Medulloblastoma 450k array (n = 763) | GEO: GSE85212 | Notebooks 02, 04 |
| Horvath multi-tissue clock (353 CpGs) | Genome Biology 14:R115 | Notebook 02 |
| lncRNA RNA-seq (6 GPL platforms) | GEO: GSE16256 | Notebook 03 |
| Roadmap 25-state ChromHMM brain (E070/E071/E081) | roadmapepigenomics.org | Notebook 02 |
| lncRNA annotations | GENCODE v44 | Notebook 03 |
| lncRNA–miRNA interactions | LncBase v3 | Notebook 03 |
| miRNA–mRNA targets | miRTarBase v9 + TargetScan v8 | Notebook 03 |

Full download instructions with exact filenames and `wget` commands: [`docs/DATA_ACCESS.md`](docs/DATA_ACCESS.md)

---

## Notebook Pipeline

```
Notebook 01                    Notebook 02
H1 ESC + IMR90 WGBS    ──→    MB 450k + Horvath clock
     │                              │
     ▼                              ▼
Meth3D-Net V6 model         Multi-axis epigenetic
Prediction metrics          features (n=763):
Instability hotspots        DNAmAge, CancerScore,
Figures S1–S3               Instability, HDAC Proxy
                            DNA Damage Index
                            Figures S4–S9
                                   │
              ┌────────────────────┘
              │
              ├──→  Notebook 03                    Notebook 04
              │     lncRNA GEO + ceRNA network     Risk model (uses
              │     lncRNA prioritisation          output of NB 02)
              │     Figure S10                     RF + XGBoost
              │                                    SHAP, KM, Cox PH
              │                                    Figures S11–S15
              └───────────────────────────────────────────────────┘
```

---

## Key Scientific Findings

**1. Genome-wide methylation prediction** — Meth3D-Net V6 achieves mean Pearson r = 0.871 across autosomes with 100% Hi-C proxy coverage, surpassing V5 and matching or exceeding published baselines in biological interpretability.

**2. Multi-axial epigenetic regulation** — The medulloblastoma epigenome is structured along largely independent axes: epigenetic ageing (DNAmAge), chromatin repression (HDAC proxy), and instability are orthogonal (r = −0.03 between instability and DNAmAge), demonstrating that tumour heterogeneity is not primarily driven by ageing.

**3. HLA locus instability hotspot** — A genome-wide maximum chromothripsis-like instability score (CT z = 4.8) at chr6:25–35 Mb encompasses HLA class I/II genes, providing mechanistic evidence for epigenetically driven immune evasion in medulloblastoma.

**4. lncRNA regulatory architecture** — TARID (via GADD45A) and DINO (via p53) are functional demethylation effectors; H19 and MALAT1 are ceRNA network hubs. The miR-145–MYC and let-7–KRAS triplets highlight novel oncogenic regulatory axes.

**5. Translatable epigenetic risk features** — EpigeneticInstability is the top-ranked predictive feature for pseudo-clinical risk classification (XGBoost AUC = 0.6551, RF CV AUC = 0.6466 ± 0.033).

---

## Citation

> Singh, N. (2026). Meth3D-Net V6: An Integrative Multi-Scale Epigenomic Framework for Methylation Prediction, Chromatin Instability Detection, and lncRNA–miRNA Regulatory Network Identification in Medulloblastoma. *[Journal Name]*. [DOI to be added upon acceptance]

---

## Reproducibility

- Random seeds fixed at `42` throughout all notebooks
- Full results table: [`docs/RESULTS_SUMMARY.md`](docs/RESULTS_SUMMARY.md)
- Processed data: Zenodo repository (DOI upon acceptance)
- All analyses conducted in Python 3.10 on Kaggle Notebooks (free tier, GPU P100)

---

## License

MIT — see [LICENSE](LICENSE). Data use is subject to the terms of the original data providers (GEO, Roadmap Epigenomics, GENCODE, LncBase, miRTarBase, TargetScan).
