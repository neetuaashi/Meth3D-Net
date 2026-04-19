# Meth3D-Net V6

**Meth3D-Net V6: Multi-Scale Epigenomic Framework for Methylation, Chromatin Instability, and lncRNA Regulation in Medulloblastoma**

*Neetu Singh — King George's Medical University (KGMC), Lucknow, India*
*neetusingh@kgmcindia.edu*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python 3.10](https://img.shields.io/badge/Python-3.10-blue.svg)](https://python.org)
[![Kaggle](https://img.shields.io/badge/Run%20on-Kaggle-20BEFF.svg)](https://kaggle.com/neetuaashi)
[![Manuscript](https://img.shields.io/badge/Manuscript-In%20Preparation-orange.svg)]()

---

## Abstract

Medulloblastoma exhibits extensive epigenetic heterogeneity not fully captured by genomic or transcriptomic analyses alone. Meth3D-Net V6 is an integrative multi-scale framework modelling DNA methylation within a three-dimensional chromatin context to uncover regulatory architecture in paediatric brain tumours. Using whole-genome bisulfite sequencing references (H1 ESC and IMR90; GSE19418) and 763 primary medulloblastoma samples (GSE85212), the model achieves robust genome-wide methylation prediction (mean Pearson r = 0.870 across autosomes) and enables inference of differential methylation blocks (DMBs), chromatin instability scores, and regulatory RNA networks.

Genome-wide instability analysis reveals a prominent hotspot at the HLA locus (chr6:25–35 Mb; CT z-score = 4.8), suggesting a mechanism for immune evasion. The tumour epigenome is organised along four largely independent axes — DNAmAge, CancerScore, EpigeneticInstability, and HDAC_Proxy — confirmed by low pairwise correlations (maximum Spearman r = 0.27).

Two-cohort independent external validation confirms V6 predictions across both disease-specific and pan-cancer contexts:
- **GSE85212** (n = 763 MB samples): fold enrichment = 1.62×, p < 2.2×10⁻³⁰⁰; Spearman r = 0.039, p = 5.9×10⁻³⁰
- **TCGA PanCancer** (n = 9,854 samples, 33 cancer types; 105,451 probes): fold = 1.36×, Spearman r = 0.271, p = 0.000

Integration with Hi-C data (GSE186599; n = 140 paediatric brain tumour samples) validates V6-predicted features: lncRNA-associated DMBs co-localise with differential chromatin loops (61.9%, p = 0.0001) and TAD boundaries (42.9%, p = 0.0044). Among six lncRNA candidates, TARID shows the highest structural support. ceRNA network analysis identifies 49 validated regulatory triplets across 4 lncRNAs, 14 lncRNA–miRNA pairs, 12 unique miRNAs, and 31 mRNA targets.

The Epigenetic Senescence Susceptibility Index (ESSI) significantly differentiates molecular subgroups (Kruskal-Wallis p = 0.0009), with epigenetic age acceleration showing a monotonic gradient across ESSI tiers (0.0% → 0.8% → 15.7%), identifying Group3 and Group4 as primary candidates for epigenetic senescence therapy.

**Availability:** All code, notebooks, and documentation are publicly available at https://github.com/neetuaashi/Meth3D-Net

---

## Repository Structure

```
Meth3D-Net/
├── notebooks/
│   ├── 01_meth3dnet_v6_genome_training.ipynb        # V6 model training, Hi-C proxy, CT scoring
│   ├── 02_methylation_pipeline_hdac.ipynb           # HDAC, DNAmAge, CancerScore, multi-axis
│   ├── 03_lncrna_geo_extraction.ipynb               # lncRNA candidate scoring (original)
│   ├── 04_pseudo_clinical_risk_model.ipynb          # ESSI, RF/XGBoost, Kaplan-Meier
│   ├── 05_tcga_validation_colab.ipynb               # TCGA pan-cancer 3-layer validation (Colab)
│   ├── 06_tcga_qarm_extractor.ipynb                 # TCGA q-arm probe extraction (Colab)
│   └── 07_lncrna_cerna_network.ipynb                # Full lncRNA–miRNA–mRNA ceRNA network
├── results/
│   ├── lncRNA_candidates.csv                        # 4 candidates: priority scores, DMB stats
│   ├── lncRNA_miRNA_pairs.csv                       # 14 lncRNA-miRNA pairs, 12 unique miRNAs
│   ├── ceRNA_triplets_validated.csv                 # All 49 ceRNA triplets
│   ├── high_priority_triplets.csv                   # 44 high-priority triplets with pathway
│   ├── layerA_summary.csv                           # TCGA Layer A validation summary
│   └── per_chromosome_validation.csv                # Per-chromosome TCGA enrichment results
├── docs/
│   ├── DATA_ACCESS.md                               # Dataset download instructions
│   ├── NOTEBOOK_GUIDE.md                            # Run order and parameter guide
│   └── RESULTS_SUMMARY.md                          # Key numerical results
├── .gitignore
├── LICENSE                                          # MIT
├── README.md
└── requirements.txt
```

> **Large result files** (probe-level matrices > 4 MB) are hosted on Kaggle, not committed here.
> See [Large Files](#large-files-on-kaggle) section below.

---

## Quick Start

```bash
git clone https://github.com/neetuaashi/Meth3D-Net.git
cd Meth3D-Net
pip install -r requirements.txt
```

Download all required datasets following [`docs/DATA_ACCESS.md`](docs/DATA_ACCESS.md), then set `LOCAL_BASE` in Cell 1 of each notebook and run in order (NB01 → NB07).

For Colab notebooks (NB05, NB06): open in Google Colab, mount Drive, and follow the path configuration in Cell 0.

---

## Notebooks

### NB01 — Meth3D-Net V6 Genome Training
`notebooks/01_meth3dnet_v6_genome_training.ipynb`

Builds Hi-C proxy features from WGBS β-values (H1 ESC + IMR90) and trains a multi-input regression model chromosome by chromosome. Evaluates prediction performance across all autosomes and sex chromosomes. Identifies genome-wide instability hotspots using chromothripsis-like (CT) scoring.

**Runtime:** ~2–4 hours (Kaggle GPU) / ~6–10 hours (CPU)

| Metric | Value |
|--------|-------|
| Mean Pearson r (autosomes) | 0.870 |
| Mean MAE | ≈ 0.101 |
| Best chromosome | chr18 (r = 0.913) |
| chrX / chrY | r = 0.786 / 0.782 |
| Genome-wide instability peak | chr6: 25–35 Mb (HLA locus, CT z = 4.8) |

---

### NB02 — Methylation Pipeline & HDAC Analysis
`notebooks/02_methylation_pipeline_hdac.ipynb`

Loads the medulloblastoma 450k array (n = 763, GSE85212) and computes five epigenetic axes: DNAmAge, CancerScore, EpigeneticInstability, HDAC_Proxy, and DNA Damage Index. Produces the OG/TSG promoter methylation landscape (120 cancer driver genes) and ESSI.

**Runtime:** ~30–60 minutes

| Axis | Key finding |
|------|-------------|
| DNAmAge | Mean 9.3 yr; 42 tumours (5.5%) epigenetically accelerated |
| CancerScore | r = 0.27 with DNAmAge (p = 6.63×10⁻¹⁴) |
| EpigeneticInstability | Orthogonal to DNAmAge (r = −0.029) |
| HDAC_Proxy | Global hypomethylation vs normal brain baseline (0.791) |

---

### NB03 — lncRNA GEO Extraction & Candidate Scoring (original)
`notebooks/03_lncrna_geo_extraction.ipynb`

Parses lncRNA expression data from GSE16256, maps lncRNA loci to proximal V6 DMBs (±2 kb), and scores candidates. Outputs the initial 4-candidate list.

---

### NB04 — Pseudo-Clinical Risk Model
`notebooks/04_pseudo_clinical_risk_model.ipynb`

Trains Random Forest and XGBoost classifiers on epigenetic features. Computes SHAP values. Runs Kaplan–Meier pseudo-survival analysis. Fits Cox proportional hazards model.

| Model | Test AUC | CV AUC (5-fold) |
|-------|----------|-----------------|
| Random Forest | 0.6339 | 0.6466 ± 0.0330 |
| XGBoost | 0.6551 | — |

---

### NB05 — TCGA PanCancer Validation *(New — Colab)*
`notebooks/05_tcga_validation_colab.ipynb`

**Run in Google Colab.** Performs three-layer validation of V6 DMB predictions in 9,854 TCGA pan-cancer samples across 33 cancer types. Reads pre-extracted probe matrix from Google Drive.

**Datasets required:**
| Dataset | Source |
|---------|--------|
| `tcga_dmb_probes_full.tsv` (12.8 GB) | Kaggle: neetuaashi/tcga-pancan-methylation |
| `humanmethylation450_15017482_v1-2.csv` | Kaggle: neetuaashi/tcga-pancan-methylation |
| V6 DMB CSV files | Kaggle: neetuaashi/methylation-paper-cpg-v6 |

**Runtime:** ~35 minutes (High RAM runtime, Google Colab)

| Layer | Test | Result |
|-------|------|--------|
| A | Variance enrichment (high vs low \|Δβ\|) | fold = 1.36×, Spearman r = 0.271, p = 0.000 |
| B | CT-z vs TCGA variance | Spearman r = 0.013, p = 4.1×10⁻⁵ |
| C | Tumour vs normal direction concordance | 40.8%, r = −0.248, p = 0.000 |

**Per-chromosome results** (top 5 by enrichment):

| Chr | n probes | Enrichment fold | Concordance % |
|-----|----------|-----------------|---------------|
| chrX | 1,666 | 1.201× | 29.35% |
| chr7 | 6,631 | 1.149× | 34.10% |
| chr8 | 4,516 | 1.139× | 30.80% |
| chr5 | 5,602 | 1.128× | 46.91% |
| chr10 | 5,485 | 1.115× | 37.37% |

---

### NB06 — TCGA Q-Arm Probe Extractor *(New — Colab)*
`notebooks/06_tcga_qarm_extractor.ipynb`

**Run in Google Colab.** Scans the raw 49.2 GB TCGA methylation matrix and extracts probes overlapping V6 q-arm DMBs only. Produces `tcga_dmb_probes_q_only.tsv` (~8.1 GB) for comparison with the full p+q extraction.

**Why q-arm only?** Q-arms contain the majority of significant DMBs (e.g., chr10_q: 7,026 vs chr10_p: 3,273). P-arms of acrocentric chromosomes (chr13/14/15/21/22) are nearly empty. The q-arm file retains ~63% of probes at ~60% of the file size.

**Runtime:** 20–60 minutes (depends on TCGA file position on Drive)

---

### NB07 — lncRNA–miRNA–mRNA ceRNA Network *(New — Updated)*
`notebooks/07_lncrna_cerna_network.ipynb`

Full ceRNA network analysis: integrates V6 DMB proximity scoring with LncBase v3, miRTarBase v9, and TargetScan v8 to build validated lncRNA–miRNA–mRNA triplets. Includes pathway enrichment, network graph generation, and TCGA PanCancer integration.

**Runtime:** ~20–40 minutes

**Key results:**

| lncRNA | Class | Priority Score | Proximal DMBs | Mean \|Δβ\| |
|--------|-------|----------------|---------------|------------|
| TARID | Demethylation | 7.28 | 5 | 0.357 |
| DINO | Demethylation | 5.82 | 3 | 0.264 |
| H19 | ceRNA | 5.18 | 2 | 0.237 |
| MALAT1 | ceRNA | 5.15 | 2 | 0.230 |

**ceRNA network summary:**
- 49 total validated triplets (44 high-priority)
- 14 lncRNA–miRNA pairs, 12 unique miRNAs
- Top pathways: EMT/metastasis (8), Pluripotency (8), RAS/MAPK (6), Cell cycle (6)
- Top mRNA targets: KRAS (4 triplets), CDK6 (4), ZEB1 (3), MYC (3), BCL2 (3)

---

## Datasets

All raw data are publicly available. Large processed files are hosted on Kaggle. See [`docs/DATA_ACCESS.md`](docs/DATA_ACCESS.md) for download instructions.

| # | Dataset | Accession | Used by |
|---|---------|-----------|---------|
| 1 | H1 ESC + IMR90 WGBS | GEO: GSE19418 | NB01 |
| 2 | Medulloblastoma 450k array (n = 763) | GEO: GSE85212 | NB02, NB04 |
| 3 | Paediatric brain tumour Hi-C (n = 140) | GEO: GSE186599 | NB01, NB07 |
| 4 | lncRNA RNA-seq (6 platforms) | GEO: GSE16256 | NB03, NB07 |
| 5 | Horvath clock coefficients | Horvath (2013) Genome Biology | NB02 |
| 6 | Roadmap 25-state ChromHMM (brain) | E070, E071, E081 | NB02 |
| 7 | GENCODE v44 lncRNA annotations | GENCODE | NB03, NB07 |
| 8–10 | LncBase v3, miRTarBase v9, TargetScan v8 | API / embedded | NB07 |
| 11 | TCGA PanCancer methylation (n = 9,854) | TCGA PanCanAtlas | NB05, NB06 |

---

## Large Files on Kaggle

Files > 4 MB are not committed to this repository. Download from Kaggle:

| File | Size | Kaggle dataset | Description |
|------|------|----------------|-------------|
| `tcga_dmb_probes_full.tsv` | 12.8 GB | [neetuaashi/tcga-pancan-methylation](https://www.kaggle.com/datasets/neetuaashi/tcga-pancan-methylation) | 105,451 DMB probes × 9,854 TCGA samples (p+q arms) |
| `tcga_dmb_probes_q_only.tsv` | 8.1 GB | [neetuaashi/tcga-pancan-methylation](https://www.kaggle.com/datasets/neetuaashi/tcga-pancan-methylation) | Q-arm probes only |
| `humanmethylation450_15017482_v1-2.csv` | 197 MB | [neetuaashi/tcga-pancan-methylation](https://www.kaggle.com/datasets/neetuaashi/tcga-pancan-methylation) | Illumina 450K manifest |
| `layerA_enrichment.csv` | 5.6 MB | [neetuaashi/methylation-data-v6](https://www.kaggle.com/datasets/neetuaashi/methylation-data-v6) | 105,451 probes: v6_abs_delta, delta_group, tcga_variance |
| `layerB_ct_replication.csv` | 4.5 MB | [neetuaashi/methylation-data-v6](https://www.kaggle.com/datasets/neetuaashi/methylation-data-v6) | 105,451 probes: ct_score_z, tcga_variance |
| `layerC_direction_concordance.csv` | 5.1 MB | [neetuaashi/methylation-data-v6](https://www.kaggle.com/datasets/neetuaashi/methylation-data-v6) | 105,451 probes: v6_delta, tcga_delta, concordant |
| V6 DMB + CT score CSVs | — | [neetuaashi/methylation-paper-cpg-v6](https://www.kaggle.com/datasets/neetuaashi/methylation-paper-cpg-v6) | All chromosome DMB and CT score matrices |

---

## Results

Key numerical results are committed in `results/`. Probe-level matrices are on Kaggle (see above).

| File | Description |
|------|-------------|
| `results/layerA_summary.csv` | Layer A: fold=1.36×, Spearman r=0.271, Mann-Whitney p=0.000 |
| `results/per_chromosome_validation.csv` | 23 chromosomes × enrichment fold, MW p-value, concordance % |
| `results/lncRNA_candidates.csv` | 4 candidates: priority scores, DMB counts, expression correlation |
| `results/lncRNA_miRNA_pairs.csv` | 14 lncRNA–miRNA pairs, 12 unique miRNAs |
| `results/ceRNA_triplets_validated.csv` | All 49 validated ceRNA triplets |
| `results/high_priority_triplets.csv` | 44 high-priority triplets with pathway annotation |

---

## Supplementary Figures

| Figure | Description | Notebook |
|--------|-------------|----------|
| S1 | Genome-wide prediction performance (Pearson r, R², MAE, Hi-C coverage) | NB01 |
| S2 | HLA locus fine-mapping — chr6:25–35 Mb | NB01 |
| S3 | Genome-wide instability landscape | NB01 |
| S4 | Horvath DNAmAge distribution (n=763; mean=9.3 yr) | NB02 |
| S5 | Cancer epigenetic signature — axis correlations | NB02 |
| S6 | Multi-axis landscape — correlation matrix, PCA | NB02 |
| S7 | DNA damage landscape | NB02 |
| S8 | OG/TSG promoter methylation heatmap (120 genes) | NB02 |
| S9 | OG/TSG methylation volcano/scatter | NB02 |
| S10 | lncRNA priority scoring — 6 candidate loci | NB07 |
| S11 | Random Forest feature importance | NB04 |
| S12 | Horvath clock CpG coverage on 450k array | NB02 |
| S13 | DNAmAge cross-validation: 270-probe vs 353-probe (r=0.955) | NB02 |
| S14 | Epigenetic age acceleration by molecular subgroup | NB02 |
| S15 | Cox proportional hazards forest plot | NB04 |
| S16 | TCGA per-chromosome enrichment (NB05 output) | NB05 |

---

## Reproducibility

- All random seeds fixed at `42` throughout all notebooks
- Python 3.10; full package versions in `requirements.txt`
- Figures saved as PNG (150 dpi) and PDF (300 dpi)
- Verified numerical results: run 18 April 2026
- TCGA validation notebooks verified on Google Colab (High RAM runtime, 25 GB)

---

## Citation

If you use Meth3D-Net V6 or any component of this framework, please cite:

> [Manuscript in preparation] Singh N. Meth3D-Net V6: Multi-Scale Epigenomic Framework for Methylation, Chromatin Instability, and lncRNA Regulation in Medulloblastoma.

**Primary dataset citation:**
> Cavalli FMG et al. (2017) Intertumoral Heterogeneity within Medulloblastoma Subgroups. *Cancer Cell* 31(6):737–754. https://doi.org/10.1016/j.ccell.2017.05.005

**Clock citation:**
> Horvath S (2013) DNA methylation age of human tissues and cell types. *Genome Biology* 14:R115. https://doi.org/10.1186/gb-2013-14-10-r115

**TCGA Pan-Cancer Atlas:**
> Liu J et al. (2018) An Integrated TCGA Pan-Cancer Clinical Data Resource. *Cell* 173:400–416. https://doi.org/10.1016/j.cell.2018.02.052

**Hi-C validation dataset:**
> Johnston M et al. (2024) Three-dimensional genome topology in paediatric brain tumours. *Cell*. https://doi.org/10.1016/j.cell.2024.06.023

---

## License

MIT — see [LICENSE](LICENSE). Data use is subject to the terms of the original data providers (GEO, Roadmap Epigenomics, TCGA, GENCODE, LncBase, miRTarBase, TargetScan).
