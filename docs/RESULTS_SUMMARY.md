# Key Results — Meth3D-Net V6

Verified numerical results from all four analysis notebooks (run 28 March 2026, random_state = 42).

---

## 1. Genome-Wide Methylation Prediction (Notebook 01)

| Metric | Value |
|--------|-------|
| Mean Pearson r (all autosomes) | **0.871** |
| Mean MAE | ≈ 0.091 |
| Mean R² range | 0.72 – 0.83 |
| Hi-C proxy coverage | **100%** (all chromosomes) |
| Best chromosome | chr19 (r = 0.913) |
| Second best | chr17 (r = 0.904) |
| Lowest autosome | chr13 (r = 0.822) |
| chrX | r = 0.788 |
| chrY | r = 0.792 |
| Genome-wide instability peak | chr6: 25–35 Mb (HLA locus, CT z = 4.8) |

---

## 2. Medulloblastoma Epigenetic Landscape (Notebook 02)

**DNAmAge (Horvath clock, n = 763)**

| Metric | Value |
|--------|-------|
| Cohort mean DNAmAge | 9.28 years |
| DNAmAge range (post-clipping) | 0.60 – 49.97 yr |
| Age acceleration threshold | 29.0 yr (mean + 2 SD) |
| Accelerated tumours | 42 (5.5%) |

**CancerScore**

| Metric | Value |
|--------|-------|
| Mean CancerScore | −0.148 |
| Range | −0.598 to +0.299 |
| 75th percentile (risk threshold) | −0.0227 |
| Correlation with DNAmAge (Spearman r) | 0.27 (p = 6.63 × 10⁻¹⁴) |

**Epigenetic Instability**

| Metric | Value |
|--------|-------|
| Correlation with DNAmAge | r = −0.03 (p = 0.43) — near-zero |
| Correlation with HDAC Proxy | r = 0.61 |
| Correlation with CancerScore | r = −0.10 |

**HDAC Proxy (Roadmap 25-state repressive chromatin)**

| Metric | Value |
|--------|-------|
| Normal brain baseline | 0.791 |
| All tumours vs baseline | Global hypomethylation in all 763 |
| Correlation with CancerScore | r = −0.23 (p = 5.37 × 10⁻¹¹) |

**DNA Damage Index**

| Metric | Value |
|--------|-------|
| Correlation with global methylation | r = 0.207 (p = 8.51 × 10⁻⁹) |
| Correlation with oncogenic balance | r = 0.018 (p = 0.618) — near-zero |

---

## 3. lncRNA–ceRNA Network (Notebook 03)

**Top lncRNA candidates**

| lncRNA | Class | Priority Score | DMBs (±2 kb) | Mean \|Δβ\| |
|--------|-------|---------------|-------------|-------------|
| TARID | Demethylation regulator | 7.3 | 5 | 0.36 |
| DINO | Demethylation regulator | 5.8 | 4 | 0.31 |
| H19 | ceRNA-active | 5.2 | 3 | 0.28 |
| MALAT1 | ceRNA-active / chromatin scaffold | 5.1 | 3 | 0.27 |

**Top ceRNA triplets**

| Triplet | Evidence |
|---------|---------|
| lncRNA–miR-145–MYC | Validated |
| lncRNA–let-7–KRAS | Validated |
| lncRNA–miR-101–EZH2 | Predicted |

**Top miRNA hub:** miR-145  
**Top pathway enrichment:** EMT/metastasis (n = 8), Pluripotency (n = 8), RAS/MAPK (n = 6), Cell cycle (n = 6)

---

## 4. Pseudo-Clinical Risk Model (Notebook 04)

**Cohort split**

| Group | n | % |
|-------|---|---|
| High-risk (CancerScore > −0.0227) | 191 | 25% |
| Low-risk | 572 | 75% |
| Train set | 610 | 80% |
| Test set | 153 | 20% |

**Classifier performance**

| Model | Test AUC | CV AUC (5-fold) | Fold scores |
|-------|----------|-----------------|-------------|
| Random Forest | 0.6339 | 0.6466 ± 0.0330 | 0.646, 0.646, 0.704, 0.637, 0.601 |
| XGBoost | **0.6551** | — | — |

**SHAP feature ranking (XGBoost)**

| Rank | Feature | Direction |
|------|---------|-----------|
| 1 | EpigeneticInstability | ↑ high-risk |
| 2 | DNAmAge | ↑ high-risk |
| 3 | AgeAcceleration | ↑ high-risk |
| 4 | IsAccelerated | binary |

**Cox proportional hazards (n = 763, events = 42)**

| Feature | Coef | HR | 95% CI | z | p |
|---------|------|----|--------|---|---|
| CancerScore | −0.25 | 0.78 | [0.21, 2.96] | −0.37 | 0.71 |
| EpigeneticInstability | −0.11 | 0.89 | [0.00, 224.44] | −0.04 | 0.97 |
| Concordance | 0.54 | | | | |
| Partial AIC | 239.41 | | | | |
| LR-test p | 0.93 | | | | |

> Wide CIs and non-significant LR-test reflect small event count (5.5% event rate) in the pseudo-survival framework. No single epigenetic axis fully captures tumour behaviour; integration of additional molecular layers and real survival endpoints is required for clinical utility.
