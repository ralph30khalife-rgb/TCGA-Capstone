# TCGA Multi-Omics Biomarker Analysis Pipeline

## Overview

This repository contains a reproducible multi-omics pipeline built around TCGA data.  
It evaluates biomarker behavior across multiple cancers through:

- Gene expression profiling  
- Survival analysis  
- Immune infiltration scoring  
- Tumor Mutation Burden (TMB)  
- Co-expression networks  
- GO and KEGG functional enrichment  

Two biomarkers were used in this study:

- **PODXL**
- **EZR**

However, the pipeline is fully generalizable — users can run it on **any biomarker** and **any TCGA cancer** by editing only:

```r
project_id <- "TCGA-XXXX"
gene_id    <- "ENSG00000XXXXX"
gene_name  <- "GENE_SYMBOL"
```

The purpose is to provide a clean, automated template for large-scale biomarker screening and future publication as a standardized analysis tool.

## Biomarkers Studied
### PODXL

A cell-adhesion glycoprotein linked to tumor invasion, metastasis, and poor prognosis.

Significant cancers (p < 0.05):

- KIRC
- KIRP
- UCEC
- PAAD

### EZR

A cytoskeleton–membrane linker involved in EMT, migration, and oncogenic signaling.

Significant cancers (p < 0.05):

- KIRC
- KIRP
- HNSC
- LIHC
- PAAD
- GBM

These cancers were used for downstream multi-omics and enrichment.

## Pipeline Modules

The project is structured into three independent R Markdown scripts.

### 1_Pipeline.Rmd — Expression & Survival

This script:

- Downloads TCGA RNA-seq STAR counts
- Extracts biomarker expression
- Normalizes via edgeR logCPM
- Produces tumor vs. normal boxplots
- Merges with clinical data
- Performs *Kaplan–Meier analysis* and *Cox proportional hazards model*

Outputs:

- Boxplot (Expression)
- KM Survival Curve
- Cox Regression CSV
- Expression Summary Table

### 2_MultiOmics.Rmd — Immune Scores + TMB

#### Immune scoring

Based on marker gene expression:

- CD8 T-cells: CD8A, CD8B
- T-cells: CD3D, CD3E, CD3G
- Leukocytes: PTPRC

Scores = Mean expression of marker genes per sample.

#### TMB

Computed from mutation data: total_mutations = number of somatic variants per tumor

Outputs:

- Immune merged CSV
- TMB merged CSV
- Scatterplots: Expression vs CD8 score / Expression vs T cells / Expression vs Leukocytes / Expression vs TMB

### 3_Enrichment.Rmd — Co-expression + GO/KEGG
#### Co-expression

For each cancer:

- Correlate biomarker vs all genes
- Keep genes where: |r| > 0.6

#### Enrichment

Using clusterProfiler:

- GO Biological Process
- KEGG Pathways

Outputs:

- Co-expressed gene tables
- GO barplots (log-scaled)
- KEGG barplots (log-scaled)
- Combined summary tables
- Cross-cancer barplot summaries

## Summary of Analysis Performed
### PODXL

Analyzed in 10 cancers.

Significant survival associations in:

- KIRC
- KIRP
- UCEC
- PAAD

Immune correlations: none strong
TMB correlations: none strong

Enrichment revealed pathways related to adhesion, cytoskeleton, and small GTPases.

### EZR

Analyzed in 10 cancers.

Significant survival associations in:

- KIRC
- KIRP
- HNSC
- LIHC
- PAAD
- GBM

No immune/TMB correlation detected.
Enrichment highlighted migration, actin cytoskeleton signaling, and adhesion pathways.

## Reproducibility

To apply the pipeline to ANY biomarker & ANY cancer:

1. Open any Rmd file.
2. Change only three variables:

``` {r}
project_id <- "TCGA-XXX"
gene_id    <- "ENSG00000XXXXX"
gene_name  <- "GENE"
```

3. Render:
``` {r}
rmarkdown::render("1_Pipeline.Rmd")
rmarkdown::render("2_MultiOmics.Rmd")
rmarkdown::render("3_Enrichment.Rmd")
```

All directories, plots, and tables are generated automatically.

## Installations
```{r}
install.packages(c("dplyr", "ggplot2", "edgeR"))

BiocManager::install(c(
  "TCGAbiolinks",
  "SummarizedExperiment",
  "clusterProfiler",
  "org.Hs.eg.db",
  "enrichplot"
))
```

## How to run the pipeline
**Step 1 — Expression & Survival**
```{r}
rmarkdown::render("1_Pipeline.Rmd")
```
**Step 2 — Multi-Omics**
```{r}
rmarkdown::render("2_MultiOmics.Rmd")
```
**Step 3 — Enrichment**
```{r}
rmarkdown::render("3_Enrichment.Rmd")
```

## Purpose of the pipeline 
The workflow is designed as a reusable research pipeline.
It requires minimal user input and supports high reproducibility.
