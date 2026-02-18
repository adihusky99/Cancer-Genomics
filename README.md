# Cancer Genomics Analysis with Maftools

**Author:** Aditya Elayavalli

A comprehensive R-based pipeline for analyzing somatic variants in cancer using the [maftools](https://bioconductor.org/packages/release/bioc/html/maftools.html) package. This analysis uses TCGA Acute Myeloid Leukemia (LAML) data as a primary example.

---

## Table of Contents

- [Overview](#overview)
- [Input Files](#input-files)
- [Installation](#installation)
- [Usage](#usage)
- [Analysis Sections](#analysis-sections)
- [References](#references)

---

## Overview

This R Markdown document walks through reading, summarizing, visualizing, and analyzing somatic mutation data stored in MAF (Mutation Annotation Format) files. It covers mutation landscape visualization, survival analysis, cohort comparisons, drug-gene interactions, and oncogenic pathway analysis.

---

## Input Files

Three types of input files are supported:

| File Type | Description | Required |
|-----------|-------------|----------|
| MAF file (`.maf.gz`) | Somatic mutation data (gzip compressed) | Yes |
| Clinical data (`.tsv`) | Per-sample survival/histology annotations | Optional |
| Copy number data | Genomic copy number information | Optional |

---

## Installation

```r
# Install maftools via Bioconductor
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("maftools")
```

---

## Usage

```r
library(maftools)

# Load MAF file and optional clinical data
laml <- read.maf(
  maf = "path/to/your.maf.gz",
  clinicalData = "path/to/clinical_data.tsv"
)
```

The `read.maf` function accepts a `vc_nonSyn` parameter to customize which variant consequences are treated as non-synonymous (high/moderate impact variants by default).

---

## Analysis Sections

### 1. Reading & Summarizing MAF Files
Loads the MAF file into a MAF object and provides accessor methods for exploring its contents:
- `getSampleSummary()` — per-sample mutation counts
- `getGeneSummary()` — per-gene mutation statistics
- `getClinicalData()` — associated clinical annotations
- `getFields()` — all available MAF columns
- `write.mafSummary()` — export summary to file

### 2. Visualization
- **MAF Summary Plot** — stacked barplot of variant counts and boxplots of variant types
- **Oncoplots** — waterfall plots for the top mutated genes (multi-hit variants flagged)
- **Transition/Transversion (TiTv) Plots** — proportions of six SNP conversion types per sample
- **Lollipop Plots** — amino acid change diagrams for individual genes (e.g., DNMT3A, TP53)
- **Rainfall Plots** — inter-mutation distance plots to identify hyper-mutated regions (kataegis detection)
- **TCGA Mutation Load Comparison** — benchmark mutation burden against 33 TCGA cohorts
- **VAF Plots** — variant allele frequency boxplots to assess clonal architecture

### 3. Somatic Interactions
Pairwise Fisher's exact tests to identify mutually exclusive or co-occurring gene mutations across the cohort.

### 4. Cancer Driver Gene Detection
Uses the **OncodriveCLUST** algorithm to identify driver genes by detecting positional clustering of mutations (hotspots).

### 5. Pfam Domain Annotation
Appends pfam domain data to amino acid changes, summarizing which protein domains are most frequently affected across the cohort.

### 6. Survival Analysis
Kaplan-Meier survival curves stratified by mutation status of user-specified genes. Includes:
- Single-gene survival (`mafSurvival`)
- Gene set–based prognostic group identification (`survGroup`, `mafSurvGroup`)

### 7. Cohort Comparison
Compares mutation frequencies between two MAF cohorts (e.g., primary vs. relapse APL) using Fisher's exact test. Visualization options include:
- Forest plots
- Co-oncoplots
- Co-barplots
- Side-by-side lollipop plots

### 8. Clinical Enrichment Analysis
Identifies mutations enriched in specific clinical subgroups (e.g., FAB classification) via groupwise and pairwise comparisons.

### 9. Drug-Gene Interactions
Queries the **Drug Gene Interaction Database (DGIdb)** to identify druggable genes and retrieve known drug-gene interaction details.

### 10. Oncogenic Signaling Pathways
Evaluates enrichment of mutations across known oncogenic signaling pathways (e.g., RTK-RAS) in TCGA cohorts.

---

## References

- Mayakonda A, Lin DC, Assenov Y, Plass C, Koeffler HP. (2018). Maftools: Efficient and Comprehensive Analysis of Somatic Variants in Cancer. *Genome Research*, 28(11), 1747–1756.
- Cancer Genome Atlas Research Network et al. (2013). Genomic and Epigenomic Landscapes of Adult de Novo Acute Myeloid Leukemia. *New England Journal of Medicine*, 368(22), 2059–2074.
