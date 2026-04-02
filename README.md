# Meth3D-Net V6

**Meth3D-Net V6: Multi-Scale Epigenomic Framework for Methylation, Chromatin Instability, and lncRNA Regulation in Medulloblastoma**

*Neetu Singh — King George's Medical University (KGMC), Lucknow, India*
*neetusingh@kgmcindia.edu*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python 3.10](https://img.shields.io/badge/Python-3.10-blue.svg)](https://python.org)
[![Kaggle](https://img.shields.io/badge/Run%20on-Kaggle-20BEFF.svg)](https://kaggle.com)



---

## Abstract

Medulloblastoma exhibits extensive epigenetic heterogeneity not fully captured by genomic or transcriptomic analyses alone. Meth3D-Net V6 is an integrative multi-scale framework modelling DNA methylation within a three-dimensional chromatin context to uncover regulatory architecture in paediatric brain tumours. Using whole-genome bisulfite sequencing references (H1 ESC and IMR90; GSE19418) and 763 primary medulloblastoma samples (GSE85212), the model achieves robust genome-wide methylation prediction (mean Pearson r = 0.870 across autosomes) and enables inference of differential methylation blocks (DMBs), chromatin instability scores, and regulatory RNA networks.

Genome-wide instability analysis reveals a prominent hotspot at the HLA locus (chr6:25–35 Mb; chromothripsis-like z-score = 4.8), suggesting a mechanism for immune evasion. The tumour epigenome is organised along four largely independent axes — epigenetic ageing (DNAmAge), oncogenic methylation balance (CancerScore), chromatin instability, and HDAC-associated repression — confirmed by low pairwise correlations (maximum Spearman r = 0.27). Epigenetic age (Horvath clock; 270/353 probes, 76.5% coverage) yields a paediatric baseline of 9.3 years, with 5.5% of samples epigenetically accelerated.

Integration with Hi-C data (GSE186599; n=140) validates V6-predicted features: lncRNA-associated DMBs co-localise with differential chromatin loops (61.9%, p=0.0001) and TAD boundaries (42.9%, p=0.0044). Among six lncRNA candidates, TARID shows highest structural support. ceRNA network analysis identifies 49 validated regulatory triplets across 4 lncRNAs, 12 miRNAs, and 31 mRNA targets.

The Epigenetic Senescence Susceptibility Index (ESSI) significantly differentiates molecular subgroups (Kruskal-Wallis p=0.0009). Epigenetic age acceleration shows a monotonic gradient across ESSI tiers (0.0%, 0.8%, 15.7%), independently validating the index and identifying Group3 and Group4 as primary candidates for epigenetic senescence therapy.

**Availability:** All code, notebooks, and documentation are publicly available at https://github.com/neetuaashi/Meth3D-Net

---

## Repository Structure

```
Meth3D-Net/
├── notebooks/
│   ├── 01_meth3dnet_v6_genome_training.ipynb
│   ├── 02_methylation_pipeline_hdac.ipynb
│   ├── 03_lncrna_geo_extraction.ipynb
│   └── 04_pseudo_clinical_risk_model.ipynb
├── docs/
│   ├── DATA_ACCESS.md
│   ├── NOTEBOOK_GUIDE.md
│   └── RESULTS_SUMMARY.md
├── requirements.txt
└── README.md
```

---

## Quick Start

```bash
git clone https://github.com/neetuaashi/Meth3D-Net.git
cd Meth3D-Net
pip install -r requirements.txt
```

Download all required datasets following [`docs/DATA_ACCESS.md`](docs/DATA_ACCESS.md), then set `LOCAL_BASE` in Cell 1 of each notebook and run in order (01 → 02 → 03 → 04).

---

## Datasets

All data are publicly available. Raw files are not hosted in this repository due to size. See [`docs/DATA_ACCESS.md`](docs/DATA_ACCESS.md) for full download instructions, file formats, and directory layout.

| # | Dataset | Accession | Used by |
|---|---------|-----------|---------|
| 1 | H1 ESC + IMR90 WGBS (Roadmap Epigenomics) | GSE19418 | Notebook 01 |
| 2 | Medulloblastoma 450k methylation array (n = 763) | GSE85212 | Notebooks 02, 04 |
| 3 | Horvath multi-tissue epigenetic clock (353 CpGs) | Horvath (2013) Genome Biology | Notebook 02 |
| 4 | lncRNA RNA-seq (6 platforms) | GSE16256 | Notebook 03 |
| 5 | Roadmap Epigenomics 25-state ChromHMM (brain tissues) | E070, E071, E081 | Notebook 02 |
| 6 | GENCODE v44 lncRNA annotations | GENCODE | Notebook 03 |
| 7–9 | LncBase v3, miRTarBase v9, TargetScan v8 | API / embedded | Notebook 03 |

Processed output files (methylation scores, epigenetic feature matrices, lncRNA candidates, ceRNA triplets) will be deposited at **Zenodo** upon manuscript acceptance.

---

## Notebooks

### Notebook 01 — Meth3D-Net V6 Genome Training

Builds Hi-C proxy features from WGBS β-values (H1 ESC + IMR90) and trains a multi-input regression model chromosome by chromosome. Evaluates prediction performance across all autosomes and sex chromosomes, and identifies genome-wide instability hotspots using chromothripsis-like (CT) scoring.

**Runtime:** ~2–4 hours (Kaggle GPU) / ~6–10 hours (CPU)  
**Saves:** Figures S1–S3

**Key results:**

| Metric | Value |
|--------|-------|
| Mean Pearson r (all autosomes) | 0.871 |
| Mean MAE | ≈ 0.091 |
| Best chromosome | chr19 (r = 0.913) |
| chrX / chrY | r = 0.788 / 0.792 |
| Genome-wide instability peak | chr6: 25–35 Mb (HLA locus, CT z = 4.8) |

---

### Notebook 02 — Methylation Pipeline & HDAC Analysis

Loads the medulloblastoma 450k array (n = 763, GSE85212) and computes five epigenetic axes: DNAmAge (Horvath clock, 353 CpGs), CancerScore (oncogene/TSG promoter methylation balance), Epigenetic Instability (rank-composite variance + entropy), HDAC Proxy (mean β at Roadmap repressive chromatin regions), and DNA Damage Index (methylation variance disruption proxy). Produces correlation analyses, OG/TSG heatmaps, and volcano plots.

**Runtime:** ~30–60 minutes  
**Saves:** Figures S4–S9

**Key results:**

| Axis | Key finding |
|------|-------------|
| DNAmAge | Cohort mean 9.3 yr; 42 tumours (5.5%) epigenetically accelerated (DNAmAge > 29.0 yr) |
| CancerScore | Mean −0.148; weakly correlated with DNAmAge (Spearman r = 0.27, p = 6.63 × 10⁻¹⁴) |
| Epigenetic Instability | Orthogonal to DNAmAge (r = −0.03, p = 0.43); correlated with HDAC Proxy (r = 0.61) |
| HDAC Proxy | Global hypomethylation vs normal brain baseline (0.791) in all 763 tumours |
| DNA Damage Index | Correlated with global methylation (r = 0.207, p = 8.51 × 10⁻⁹); near-zero with CancerScore (r = 0.018) |

**Epigenetic age acceleration by molecular subgroup:**

| Subgroup | n | Accelerated (DNAmAge > 29.0 yr) |
|----------|---|----------------------------------|
| WNT | 70 | 2 (2.9%) |
| SHH | 223 | 9 (4.0%) |
| Group3 | 144 | 11 (7.6%) |
| Group4 | 326 | 20 (6.1%) |

Group3 shows the highest proportion of epigenetically accelerated tumours; the directional trend across subgroups (WNT → SHH → Group3 → Group4) is biologically interpretable but does not reach statistical significance by Kruskal–Wallis test. Epigenetic age acceleration is therefore characterised as a supporting observation rather than a primary finding.

**Clock validation (Supplementary Figures S12–S14):**  
Of 353 Horvath clock CpGs, 270 (76.5%) are present on the 450k array; 83 absent probes were mean-imputed. Cross-validation against the full 353-probe estimate yields r = 0.9552 (n = 763, MAE = 18.6 yr, reflecting systematic downward bias from missing probes at high ages). Per-probe |r| with DNAmAge has a mean of 0.137, confirming that clock accuracy is an ensemble property rather than a property of any individual CpG.

---

### Notebook 03 — lncRNA GEO Extraction & ceRNA Network

Parses lncRNA expression data from GSE16256 (6 GPL platforms), maps lncRNA loci to proximal differentially methylated blocks (DMBs, ±2 kb), scores candidates by DMB density, |Δβ|, and functional evidence, queries LncBase v3 for lncRNA–miRNA interactions, integrates miRTarBase v9 and TargetScan v8 for miRNA–mRNA targets, and constructs the full lncRNA–miRNA–mRNA ceRNA network.

**Runtime:** ~20–40 minutes  
**Saves:** Figure S10

**Top lncRNA candidates:**

| lncRNA | Class | Priority Score | DMBs (±2 kb) | Mean \|Δβ\| |
|--------|-------|---------------|-------------|-------------|
| TARID | Demethylation regulator | 7.3 | 5 | 0.36 |
| DINO | Demethylation regulator | 5.8 | 4 | 0.31 |
| H19 | ceRNA-active | 5.2 | 3 | 0.28 |
| MALAT1 | ceRNA-active / chromatin scaffold | 5.1 | 3 | 0.27 |

**Top ceRNA triplets:**

| Triplet | Evidence level |
|---------|----------------|
| lncRNA–miR-145–MYC | Validated |
| lncRNA–let-7–KRAS | Validated |
| lncRNA–miR-101–EZH2 | Predicted |

Top miRNA hub: miR-145. Top pathway enrichments: EMT/metastasis (n = 8), Pluripotency (n = 8), RAS/MAPK (n = 6), Cell cycle (n = 6).

---

### Notebook 04 — Pseudo-Clinical Risk Model

Loads the epigenetic feature matrix from Notebook 02 and defines a high-risk group (CancerScore > 75th percentile, threshold = −0.0227; 191/763 tumours, 25%). Trains Random Forest and XGBoost classifiers (4 epigenetic features; 80/20 split; 5-fold CV), computes SHAP values for feature attribution, runs Kaplan–Meier pseudo-survival analysis (DNAmAge as time proxy), and fits a Cox proportional hazards model.

**Runtime:** ~10–20 minutes  
**Saves:** Figures S11–S15

**Classifier performance:**

| Model | Test AUC | CV AUC (5-fold) |
|-------|----------|-----------------|
| Random Forest | 0.6339 | 0.6466 ± 0.0330 |
| XGBoost | 0.6551 | — |

**SHAP feature ranking (XGBoost):** EpigeneticInstability > DNAmAge > AgeAcceleration > IsAccelerated.

**Cox model (n = 763, events = 42):** Concordance = 0.54; LR-test p = 0.93. Wide confidence intervals and non-significant likelihood ratio test reflect the small event count (5.5% event rate) inherent to the pseudo-survival framework. These results are presented as exploratory; real survival endpoints and additional molecular layers are required for clinical translation.

---

## Supplementary Figures

| Figure | Description | Notebook |
|--------|-------------|----------|
| S1 | Genome-wide prediction performance (Pearson r, R², MAE, Hi-C coverage) | 01 |
| S2 | HLA locus epigenetic analysis — chr6:25–35 Mb | 01 |
| S3 | Genome-wide instability landscape (DMB density, CT instability heatmap) | 01 |
| S4 | Horvath DNAmAge distribution (n = 763; mean = 9.3 yr) | 02 |
| S5 | Cancer epigenetic signature — DNAmAge vs CancerScore, Instability vs Age | 02 |
| S6 | Multi-axis landscape — correlation matrix, PCA, HDAC vs CancerScore | 02 |
| S7 | Tumour-wide DNA damage landscape | 02 |
| S8 | Oncogene and TSG promoter methylation heatmap (120 genes) | 02 |
| S9 | Oncogene and TSG methylation volcano scatter plot | 02 |
| S10 | lncRNA candidate prioritisation — priority scores, DMB density, pathway enrichment, mRNA targets | 03 |
| S11 | Random Forest feature importance | 04 |
| S12 | Horvath clock CpG coverage and validation on 450k array | 02 |
| S13 | DNAmAge cross-validation: 270-probe vs 353-probe estimate | 02 |
| S14 | Epigenetic age acceleration by molecular subgroup | 02 |
| S15 | Cox proportional hazards forest plot | 04 |

---

## Reproducibility

- All random seeds fixed at `42` throughout all notebooks
- Python 3.10; full package versions in `requirements.txt`
- Figures saved as PNG (150 dpi) and PDF (300 dpi)
- Verified numerical results: run 28 March 2026

---

## Citation

If you use Meth3D-Net V6 or any component of this framework, please cite:

> [Manuscript in preparation] Meth3D-Net V6: Multi-Scale Epigenomic Framework for Methylation, Chromatin Instability, and lncRNA Regulation in Medulloblastoma.

Primary dataset citation:  
> Cavalli FMG et al. (2017) Intertumoral Heterogeneity within Medulloblastoma Subgroups. *Cancer Cell* 31(6):737–754. https://doi.org/10.1016/j.ccell.2017.05.005

Clock citation:  
> Horvath S (2013) DNA methylation age of human tissues and cell types. *Genome Biology* 14:R115. https://doi.org/10.1186/gb-2013-14-10-r115

---

## License

This repository is released for academic use. See `LICENSE` for details.


## License

MIT — see [LICENSE](LICENSE). Data use is subject to the terms of the original data providers (GEO, Roadmap Epigenomics, GENCODE, LncBase, miRTarBase, TargetScan).
