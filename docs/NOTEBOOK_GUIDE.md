# Notebook Guide — Meth3D-Net V6

This guide explains how to run each of the four analysis notebooks, in order.  
All notebooks are self-contained and portable across Kaggle and local environments.

---

## Quick Start

```bash
git clone https://github.com/neetuaashi/Meth3D-Net.git
cd Meth3D-Net
pip install -r requirements.txt
```

Download the required datasets (see [`docs/DATA_ACCESS.md`](DATA_ACCESS.md)), then run the notebooks in order.

---

## Notebook 01 — Meth3D-Net V6 Genome Training

**File:** `notebooks/01_meth3dnet_v6_genome_training.ipynb`  
**Saves:** Figures S1, S2, S3  
**Runtime:** ~2–4 hours on Kaggle GPU / ~6–10 hours CPU

### What it does
- Builds Hi-C proxy features from WGBS β-values (H1 ESC + IMR90, GSE19418)
- Trains the Meth3D-Net V6 multi-input regression model chromosome by chromosome
- Evaluates performance (Pearson r, R², MAE) across all autosomes and sex chromosomes
- Identifies genome-wide instability hotspots using chromothripsis-like (CT) scoring
- Detects the HLA locus instability peak (chr6, z = 4.8)

### Configuration (Cell 1)
```python
# Set your data directory:
LOCAL_BASE = "/path/to/your/data"
# Expected files:
# {LOCAL_BASE}/GSE19418_H1_WGBS_CpG_beta_values.txt
# {LOCAL_BASE}/GSE19418_IMR90_WGBS_CpG_beta_values.txt
```

### Key outputs
| Figure | Description |
|--------|-------------|
| S1 | Genome-wide prediction performance (Pearson r, R², MAE, Hi-C coverage) |
| S2 | HLA locus epigenetic analysis — chr6:25–35 Mb |
| S3 | Genome-wide instability landscape (DMB density, CT instability heatmap) |

---

## Notebook 02 — Methylation Pipeline & HDAC Analysis

**File:** `notebooks/02_methylation_pipeline_hdac.ipynb`  
**Saves:** Figures S4–S9  
**Runtime:** ~30–60 minutes

### What it does
- Loads medulloblastoma 450k methylation data (n = 763, GSE85212)
- Estimates DNAmAge using the Horvath multi-tissue clock (353 CpGs)
- Computes multi-axis epigenetic features:
  - CancerScore (OncoScore − TSGScore across 120 OG/TSG loci)
  - Epigenetic Instability (rank-composite variance + entropy)
  - HDAC Proxy (mean β at Roadmap 25-state repressive chromatin regions)
  - DNA Damage Index (variance-based methylation disruption proxy)
- Produces correlation analyses across all epigenetic axes
- Generates OG/TSG heatmap and volcano plots

### Configuration (Cell 1)
```python
LOCAL_BASE = "/path/to/your/data"
# Expected files:
# {LOCAL_BASE}/GSE85212_Methylation_763samples_SubtypeStudy_TaylorLab_beta_values.txt
# {LOCAL_BASE}/GSE85212_series_matrix.txt
# {LOCAL_BASE}/gb-2013-14-10-r115-S3.csv          (Horvath clock)
# {LOCAL_BASE}/E070_25_imputed12marks_hg38lift_mnemonics.bed
# {LOCAL_BASE}/E071_25_imputed12marks_hg38lift_mnemonics.bed
# {LOCAL_BASE}/E081_25_imputed12marks_mnemonics.bed
```

### Key outputs
| Figure | Description |
|--------|-------------|
| S4 | Horvath DNAmAge distribution (n = 763; mean = 9.3 yr) |
| S5 | Cancer epigenetic signature — DNAmAge vs CancerScore, Instability vs Age |
| S6 | Multi-axis landscape — correlation matrix, PCA, HDAC vs CancerScore |
| S7 | Tumour-wide DNA damage landscape |
| S8 | Oncogene and TSG promoter methylation heatmap (120 genes) |
| S9 | Oncogene and TSG methylation volcano scatter plot |

---

## Notebook 03 — lncRNA GEO Extraction & ceRNA Network

**File:** `notebooks/03_lncrna_geo_extraction.ipynb`  
**Saves:** Figure S10  
**Runtime:** ~20–40 minutes (longer if downloading GEO data fresh)

### What it does
- Parses lncRNA expression data from GEO (GSE16256, 6 GPL platform files)
- Maps lncRNA loci to proximal DMBs (within 2 kb of RaM-significant sites)
- Computes priority scores (DMB density + |Δβ| + functional evidence)
- Queries LncBase v3 for lncRNA–miRNA interactions (with embedded fallback)
- Integrates miRTarBase v9 + TargetScan v8 for miRNA–mRNA targets
- Constructs the lncRNA–miRNA–mRNA ceRNA network
- Identifies high-confidence triplets (lncRNA–miR-145–MYC, lncRNA–let-7–KRAS, etc.)

### Configuration (Cell 1)
```python
LOCAL_BASE = "/path/to/your/data"
# Expected files (GSE16256, all 6 GPL platforms):
# {LOCAL_BASE}/GSE16256-GPL10999_series_matrix.txt
# {LOCAL_BASE}/GSE16256-GPL11154_series_matrix.txt
# {LOCAL_BASE}/GSE16256-GPL13393_series_matrix.txt
# {LOCAL_BASE}/GSE16256-GPL16791_series_matrix.txt
# {LOCAL_BASE}/GSE16256-GPL9052_series_matrix.txt
# {LOCAL_BASE}/GSE16256-GPL9115_series_matrix.txt
# {LOCAL_BASE}/gencode.v44.long_noncoding_RNAs.gtf   (GENCODE v44)
```

### Key outputs
| Figure | Description |
|--------|-------------|
| S10 | lncRNA candidate prioritisation — priority scores, DMB density, pathway enrichment, mRNA targets |

---

## Notebook 04 — Pseudo-Clinical Risk Model

**File:** `notebooks/04_pseudo_clinical_risk_model.ipynb`  
**Saves:** Figures S11–S15  
**Runtime:** ~10–20 minutes

### What it does
- Loads the epigenetic feature matrix produced by Notebook 02
- Defines high-risk group: CancerScore > 75th percentile (threshold = −0.0227)
- Trains Random Forest and XGBoost classifiers (4 features; 80/20 split; 5-fold CV)
- Computes SHAP values (TreeExplainer) for feature attribution
- Runs Kaplan–Meier pseudo-survival analysis (DNAmAge as time proxy)
- Fits Cox proportional hazards model (CancerScore + EpigeneticInstability)

### Configuration (Cell 1)
```python
LOCAL_BASE = "/path/to/your/data"
# Expected file (output of Notebook 02):
# {LOCAL_BASE}/Final_Methylation_Scores.csv
```

### Verified results (run 28 March 2026, random_state = 42)
| Metric | Value |
|--------|-------|
| RF test AUC | 0.6339 |
| RF CV AUC (5-fold) | 0.6466 ± 0.0330 |
| XGBoost test AUC | 0.6551 |
| Cox concordance | 0.54 |
| Cox partial AIC | 239.41 |
| Top SHAP feature | EpigeneticInstability |

### Key outputs
| Figure | Description |
|--------|-------------|
| S11 | RF feature importance — EpigeneticInstability ranks highest |
| S12 | ROC curves — RF (AUC = 0.634) vs XGBoost (AUC = 0.655) |
| S13 | SHAP summary plot — XGBoost feature contributions |
| S14 | Kaplan–Meier pseudo-survival — high vs low risk groups |
| S15 | Cox proportional hazards forest plot — HR with 95% CI |

---

## Bug Fixes Applied

All notebooks have been audited and corrected. The following bugs were fixed before deposition:

| Notebook | Bug | Fix |
|----------|-----|-----|
| 04 | CancerScore threshold hardcoded at 0.7 → gave 0 high-risk samples | Changed to `quantile(0.75)` → correct threshold −0.0227 |
| 04 | `roc_auc_score` import missing | Moved to top-of-notebook imports |
| 04 | 3 `plt.savefig()` calls missing (Figures S11, S14, S15) | Added with correct paths |
| 04 | Interactive matplotlib backend caused errors | Added `matplotlib.use('Agg')` |
| 04 | `cox_df` used before defined (NameError) | Merged cells 41+42 |
| 02 | 5 hardcoded `/kaggle/input` paths | Replaced with portable config block |
| 03 | 3 hardcoded `/kaggle/input` paths | Replaced with portable config block |

---

## Reproducibility

- All random seeds fixed at `42` throughout
- Python 3.10, package versions in `requirements.txt`
- Figures saved as both PNG (150 dpi, for display) and PDF (300 dpi, for publication)
