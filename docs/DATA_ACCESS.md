# Data Access Guide — Meth3D-Net V6

All datasets used in this study are publicly available. Raw data are **not** hosted in this repository due to file size. This guide explains exactly where to download each file and where to place it.

---

## Recommended Directory Layout

```
data/
├── wgbs/
│   ├── GSE19418_H1_WGBS_CpG_beta_values.txt
│   └── GSE19418_IMR90_WGBS_CpG_beta_values.txt
├── methylation/
│   ├── GSE85212_Methylation_763samples_SubtypeStudy_TaylorLab_beta_values.txt
│   └── GSE85212_series_matrix.txt
├── clock/
│   └── gb-2013-14-10-r115-S3.csv
├── lncrna/
│   ├── GSE16256-GPL10999_series_matrix.txt
│   ├── GSE16256-GPL11154_series_matrix.txt
│   ├── GSE16256-GPL13393_series_matrix.txt
│   ├── GSE16256-GPL16791_series_matrix.txt
│   ├── GSE16256-GPL9052_series_matrix.txt
│   ├── GSE16256-GPL9115_series_matrix.txt
│   └── gencode.v44.long_noncoding_RNAs.gtf
└── chromhmm/
    ├── E070_25_imputed12marks_hg38lift_mnemonics.bed
    ├── E070_25_imputed12marks_mnemonics.bed
    ├── E071_25_imputed12marks_hg38lift_mnemonics.bed
    └── E081_25_imputed12marks_mnemonics.bed
```

Set `LOCAL_BASE = "/path/to/data"` in Cell 1 of each notebook, then each notebook will auto-detect the correct paths.

---

## Dataset 1 — H1 ESC & IMR90 WGBS (Roadmap Epigenomics)

**GEO Accession:** GSE19418  
**URL:** https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE19418

**Samples to download:**
| Sample | Description | GEO ID |
|--------|-------------|--------|
| H1 ESC WGBS replicate 1 | Human embryonic stem cells | GSM429321 |
| H1 ESC WGBS replicate 2 | Human embryonic stem cells | GSM429322 |
| IMR90 WGBS replicate 1 | Lung fibroblasts | GSM432687 |
| IMR90 WGBS replicate 2 | Lung fibroblasts | GSM432688 |

**Download command (GEO FTP):**
```bash
# H1 ESC
wget "ftp://ftp.ncbi.nlm.nih.gov/geo/samples/GSM429nnn/GSM429321/suppl/GSM429321_*.txt.gz"
wget "ftp://ftp.ncbi.nlm.nih.gov/geo/samples/GSM429nnn/GSM429322/suppl/GSM429322_*.txt.gz"

# IMR90
wget "ftp://ftp.ncbi.nlm.nih.gov/geo/samples/GSM432nnn/GSM432687/suppl/GSM432687_*.txt.gz"
wget "ftp://ftp.ncbi.nlm.nih.gov/geo/samples/GSM432nnn/GSM432688/suppl/GSM432688_*.txt.gz"
gunzip *.txt.gz
```

**Format:** Tab-separated CpG-level β-values, GRCh38/hg38  
**Used by:** Notebook 01

---

## Dataset 2 — Medulloblastoma 450k Methylation Array

**GEO Accession:** GSE85212  
**URL:** https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE85212  
**Citation:** Cavalli et al. (2017) Cancer Cell 31(6):737–754

**Files to download:**
```bash
wget "ftp://ftp.ncbi.nlm.nih.gov/geo/series/GSE85nnn/GSE85212/suppl/GSE85212_Methylation_763samples_SubtypeStudy_TaylorLab_beta_values.txt.gz"
wget "https://ftp.ncbi.nlm.nih.gov/geo/series/GSE85nnn/GSE85212/matrix/GSE85212_series_matrix.txt.gz"
gunzip *.gz
```

**Format:** 450k Illumina array β-values; 321k probes after QC; n = 763 tumours  
**Used by:** Notebooks 02, 04

---

## Dataset 3 — Horvath Multi-Tissue Epigenetic Clock

**Source:** Supplementary File S3 of Horvath (2013) Genome Biology 14:R115  
**DOI:** https://doi.org/10.1186/gb-2013-14-10-r115  
**Direct download:**
```bash
wget "https://static-content.springer.com/esm/art%3A10.1186%2Fgb-2013-14-10-r115/MediaObjects/13059_2013_3156_MOESM3_ESM.csv" \
     -O gb-2013-14-10-r115-S3.csv
```

**Format:** CSV with 353 CpG probe IDs, β-value coefficients, and intercept  
**Used by:** Notebook 02

---

## Dataset 4 — lncRNA RNA-seq (GSE16256, 6 platforms)

**GEO Accession:** GSE16256  
**URL:** https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE16256

**Download all 6 series matrix files:**
```bash
for GPL in GPL10999 GPL11154 GPL13393 GPL16791 GPL9052 GPL9115; do
    wget "https://ftp.ncbi.nlm.nih.gov/geo/series/GSE16nnn/GSE16256/matrix/GSE16256-${GPL}_series_matrix.txt.gz"
done
gunzip GSE16256-*.gz
```

**Format:** GEO Series Matrix format; platform-specific probe IDs  
**Used by:** Notebook 03

---

## Dataset 5 — Roadmap Epigenomics 25-State ChromHMM (Brain)

**Source:** Roadmap Epigenomics Consortium  
**URL:** https://egg2.wustl.edu/roadmap/data/byFileType/chromhmmSegmentations/ChmmModels/imputed12marks/jointModel/final/

**Download the 4 brain tissue BED files:**
```bash
# Hippocampus (E070) — hg38 lifted
wget "https://egg2.wustl.edu/roadmap/data/byFileType/chromhmmSegmentations/ChmmModels/imputed12marks/jointModel/final/E070_25_imputed12marks_hg38lift_mnemonics.bed.gz"

# Hippocampus (E070) — hg19
wget "https://egg2.wustl.edu/roadmap/data/byFileType/chromhmmSegmentations/ChmmModels/imputed12marks/jointModel/final/E070_25_imputed12marks_mnemonics.bed.gz"

# Inferior Temporal Lobe (E071) — hg38 lifted
wget "https://egg2.wustl.edu/roadmap/data/byFileType/chromhmmSegmentations/ChmmModels/imputed12marks/jointModel/final/E071_25_imputed12marks_hg38lift_mnemonics.bed.gz"

# Fetal Brain Male (E081) — hg19
wget "https://egg2.wustl.edu/roadmap/data/byFileType/chromhmmSegmentations/ChmmModels/imputed12marks/jointModel/final/E081_25_imputed12marks_mnemonics.bed.gz"

gunzip *.bed.gz
```

**ChromHMM states used:** ReprPC, ReprPCWk, Het, Quies, TssBiv, BivFlnk (repressive/bivalent)  
**Used by:** Notebook 02

---

## Dataset 6 — GENCODE v44 lncRNA Annotations

**Source:** GENCODE Project  
**URL:** https://www.gencodegenes.org/human/release_44.html

```bash
wget "https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_44/gencode.v44.long_noncoding_RNAs.gtf.gz"
gunzip gencode.v44.long_noncoding_RNAs.gtf.gz
```

**Used by:** Notebook 03

---

## Datasets 7–9 — Interaction Databases (API / web)

These are accessed programmatically within Notebook 03 and do not require manual download.

| Database | Access method | Fallback |
|----------|---------------|---------|
| LncBase v3 (lncRNA–miRNA) | REST API: `https://lncbase.diana.gr` | Embedded curated table in notebook |
| miRTarBase v9 (miRNA–mRNA validated) | Embedded in notebook | — |
| TargetScan v8 (miRNA–mRNA predicted) | Embedded in notebook | — |

---

## Processed Data (Zenodo)

Processed output CSV files (methylation scores, epigenetic feature matrices, lncRNA candidates, ceRNA triplets) will be deposited at **Zenodo** upon manuscript acceptance. The DOI will be added here at that time.

Files that will be available:
- `Final_Methylation_Scores.csv`
- `Final_DNAmAge_Robust.csv`
- `MB_epigenetic_metrics.csv`
- `MB_full_epigenetic_with_DNA_damage.csv`
- `lncRNA_candidates.csv`
- `high_priority_triplets.csv`
- `network_hub_analysis.csv`
- `Onco_TSG_CT_Scores.xlsx`
