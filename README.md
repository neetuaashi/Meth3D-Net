# Meth3D-Net V6

## 🧬 Overview
Meth3D-Net V6 is a multi-scale deep learning framework for genome-wide DNA methylation prediction and integrative epigenomic analysis.

The model combines:
- CpG-level methylation context  
- Genomic positional features  
- 3D chromatin structure (Hi-C proxy signals)  

and extends to biological interpretation through chromatin instability detection and lncRNA–miRNA regulatory network analysis.

---

## 🔬 Key Contributions

- Genome-wide methylation prediction (**Pearson r ≈ 0.87**)
- Multi-axis epigenetic landscape:
  - Epigenetic ageing  
  - Chromatin repression  
  - Epigenetic instability  
- Chromothripsis-like instability detection  
- lncRNA–miRNA–mRNA (ceRNA) network integration  
- Pseudo-clinical risk modeling  

---

## 📊 Datasets

Raw WGBS data are available from GEO:

GSE19418

Samples used:
- GSM429321 (H1 ESC)
- GSM429322 (H1 ESC)
- GSM432687 (IMR90)
- GSM432688 (IMR90)
Due to large file sizes, raw data are not hosted in this repository. 
- Medulloblastoma methylation — GEO: GSE85212  
- lncRNA expression — GEO: GSE16256  
- Roadmap Epigenomics ChromHMM (E070, E071, E081)

---

## 📁 Repository Structure

