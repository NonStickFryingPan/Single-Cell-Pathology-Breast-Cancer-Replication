<div align="center">

# 🧬 Single-Cell Pathology Breast Cancer Replication

**A Colab-friendly reproduction of Jackson et al. 2020, focused on cell phenotypes, spatial tissue structure, and survival analysis.**

[![Notebook](https://img.shields.io/badge/Notebook-Google%20Colab-F9AB00?style=flat-square&logo=googlecolab)](SurvivalAnalysis_github.ipynb)
[![Language](https://img.shields.io/badge/Language-R-276DC3?style=flat-square&logo=r)](https://www.r-project.org/)
[![Paper](https://img.shields.io/badge/Paper-Nature-000000?style=flat-square)](https://doi.org/10.1038/s41586-019-1876-x)
[![Data](https://img.shields.io/badge/Data-Zenodo-1682D4?style=flat-square)](https://doi.org/10.5281/zenodo.3518284)

[Overview](#overview) · [Results](#results) · [Figure Guide](#beginner-figure-guide) · [Key Outputs](#key-text-outputs) · [Run](#run-the-notebook) · [Glossary](#glossary)

</div>

---

## Overview

This project is a methodological replication of:

Hartland W. Jackson, Jana R. Fischer, Vito R. T. Zanotelli, et al. **"The single-cell pathology landscape of breast cancer."** Nature 578, 615-620 (2020). DOI: https://doi.org/10.1038/s41586-019-1876-x

The original study used **imaging mass cytometry** to measure many protein markers in breast cancer tissue at single-cell resolution. Instead of treating a tumor as one bulk sample, the paper looked at individual cells, grouped them into phenotypes, measured where those cells sit in tissue, and tested whether those patterns relate to patient survival.

This notebook does not rerun the full raw image-processing pipeline. That original pipeline is too large for a simple Google Colab demonstration. Instead, this project reproduces the core analysis logic using the public data and the paper's released phenotype labels where full recomputation would be too heavy.

## The Paper In Plain English

Imagine a tumor tissue slide as a city map.

- Each cell is one building.
- Each protein marker is a label on that building.
- Similar buildings form neighborhoods, or cell phenotypes.
- The X/Y coordinates tell us where each building sits in the city.

Basically, the question the paper is looking at is: do certain city layouts correlate with better or worse patient outcomes? The main idea of the paper is that cancer isn’t just about which cells are there, but also how they’re arranged and which cell communities take over the tumor.  

## What We Reproduced

The notebook follows this workflow:

```text
Single-cell marker data
        |
        v
Complete-cell sampling
        |
        v
Cell-by-marker matrix + spatial coordinates
        |
        v
Published PhenoGraph phenotype labels
        |
        v
Phenotype abundance per tissue core
        |
        v
Survival analysis, spatial mixing, and Basel-vs-Zurich comparison
```

The notebook runs a small PhenoGraph clustering demonstration on sampled Basel cells. That demo is intentionally smaller than the full paper pipeline, but it shows how marker-expression profiles can become cell phenotype clusters.

## Repository Files

| File | Purpose |
| --- | --- |
| `SurvivalAnalysis_github.ipynb` | Cleaned notebook with outputs preserved. |
| `images/` | Extracted PNG figures from the notebook outputs. |
| `README.md` | Project explanation, figure guide, key outputs, and run guide. |

## Results

The final Colab run analyzed 100,000 complete sampled cells from Basel. Basel was the main survival cohort. Zurich was used for independent phenotype and spatial validation.

| Metric | Basel | Zurich |
| --- | ---: | ---: |
| Complete sampled cells | 100,000 | 100,000 |
| Wide cell table | 100,000 x 67 | 100,000 x 71 |
| Paper phenotype labels matched | 100,000 | 92,642 |
| Phenotypes available | 71 | 29 Basel-style matched phenotypes |
| Metadata/abundance records | 376 | 347 |
| Survival analysis | Yes | No usable merged survival rows |
| FDR-significant survival phenotypes | 0 / 71 | N/A |
| Spatial demo cores | 5 | 5 |
| Mean spatial mixing score | 0.669 | 0.757 |

The survival screen tested all 71 Basel phenotypes against overall survival. 

## Limitations:

This demo only uses 10,000 for clustering of the 1.7 million available cells. That's enough to show how the method works, but not enough to trust the cluster numbers.



> Rare cell types get lost and the survival statistics become unreliable. We tried using the actual the pre-computed data from the paper for survival and t-SNE results in this notebook to avoid this problem.

## Figures

The images below were extracted directly from `SurvivalAnalysis_github.ipynb`.

### Figure 1: Data Funnel

![Data funnel](images/figure_01_data-funnel.png)

A bar chart tracing the dataset from raw long-format marker rows down to analysis-ready cells, spatial matches, paper cluster labels, and t-SNE matches. The numbers shrink sharply at each step because the original file stores one cell across many marker rows — which is exactly why we had to sample **complete cells**, not random rows.

### Figure 2: Marker Expression Distributions

![Marker expression distributions](images/figure_02_marker-expression-distributions.png)

One histogram per protein marker. Wide spreads mean high cell-to-cell variability; narrow ones mean cells look similar on that marker. PhenoGraph needs variation to cluster on, and these distributions confirm there's enough of it.

### Figure 3: Demo Cluster Marker Heatmap

![Demo cluster marker heatmap](images/figure_03_demo-cluster-marker-heatmap.png)

Rows are demo PhenoGraph clusters; columns are protein markers. Red = high expression, blue = low. Each row's color pattern is that cluster's marker fingerprint — how a bare cluster number becomes an interpretable cell phenotype.

### Figure 4: t-SNE Cell Landscape

![t-SNE cell landscape](images/figure_04_t-sne-cell-landscape.png)

Each dot is one cell, positioned so similar cells sit near each other. Color shows the paper's PhenoGraph phenotype label. Separated islands usually represent different cell states; color patches show where each phenotype concentrates. This recreates the paper's cell atlas — many cells compressed into a 2D map.

### Figure 5: Phenotype Abundance Bar Plot

![Phenotype abundance bar plot](images/figure_05_phenotype-abundance-bar-plot.png)

Basel phenotypes ranked by their average percentage across tissue cores. This is where single-cell labels become a patient-level feature: survival analysis runs on phenotype abundance, not on individual cells.

### Figure 6: Kaplan-Meier Survival Curves

![Kaplan-Meier survival curves](images/figure_06_kaplan-meier-survival-curves.png)

Each panel compares survival between patients with high vs low abundance of one phenotype. X-axis is time in months, y-axis is estimated survival probability, and the p-value tests whether the two groups differ. A curve that drops faster means more deaths happened earlier. It directly asks: does having more of a certain cell type correlate with living longer or shorter?

> **Note:** Cluster names in the panel titles are truncated — a Google Colab rendering limitation, not a data issue. The full cluster IDs are preserved in the underlying analysis.

### Figure 7: Hazard-Ratio Forest Plot

![Hazard ratio forest plot](images/figure_07_hazard-ratio-forest-plot.png)

Each point is a phenotype's Cox-model hazard ratio on a log scale, with horizontal confidence intervals. Points right of center suggest higher risk; points left suggest lower risk; lines crossing the vertical zero line are indistinguishable from no effect. In this run, none of the associations survived FDR correction.

### Figure 8: Spatial Cell Map

![Spatial cell map](images/figure_08_spatial-cell-map.png)

Real cell locations in one Basel tissue core, colored by phenotype. Rarer phenotypes are collapsed into grey `Other` to keep the legend readable. Mixed-color areas show phenotype intermingling; areas dominated by one color show local enrichment. If you've wondered why this is called *spatial* pathology rather than just pathology, this figure is the answer.

### Figure 9: Spatial Mixing, Basel vs Zurich

![Spatial mixing Basel vs Zurich](images/figure_09_spatial-mixing-basel-vs-zurich.png)

Higher mixing score = a cell's neighbors are often different phenotypes. Lower = phenotypes are clumped. Zurich scores higher in this demo, suggesting less spatial segregation than Basel — one simple way to quantify tissue architecture without doing anything fancy.

### Figure 10: Cohort Contribution Dashboard

![Cohort contribution dashboard](images/figure_10_cohort-contribution-dashboard.png)

A tile plot summarizing what each cohort contributed. Basel has phenotypes, spatial coordinates, and usable survival data. Zurich has phenotypes and spatial coordinates but no usable overall survival rows after merging, so it supports validation only, not survival reproduction.

---

## Key Text Outputs

### Package Setup

The notebook installs R packages for table processing, plotting, survival analysis, nearest-neighbor spatial analysis, and PhenoGraph clustering. `Rphenograph` is installed from GitHub because it wasn't available on CRAN for the Colab R version.

### Complete-Cell Sampling

| Cohort | Long-format rows | Unique cells |
| --- | ---: | ---: |
| Basel | 6,400,000 | 100,000 |
| Zurich | 6,800,000 | 100,000 |

The first broken approach sampled random rows and produced incomplete cells. The fix: sample cell IDs first, then keep all marker rows for those cells.

### Basel Master Table

- 100,000 cells, 73 columns
- 100,000 cells with spatial X/Y and paper PhenoGraph labels
- 19,838 cells matched to paper t-SNE coordinates

### Independent PhenoGraph Demo

Run on 10,000 sampled Basel cells, 33 marker columns, k = 20. Produced 30 demo clusters. Adjusted Rand Index vs paper labels: **0.131**.

The low ARI is expected. The demo uses a small sample with simplified preprocessing; the paper ran a full-scale pipeline. The goal is to demonstrate the method, not reproduce the paper's exact clusters.

### Basel Survival Screen

- 376 Basel records, 102 death events, 71 phenotypes tested
- Top raw associations: Clusters 43, 15, 68, 34, 54
- **FDR-significant phenotypes: 0 / 71**

Some raw p-values looked interesting. None survived multiple-testing correction. That's the actual result from a downsampled reproduction.

### Multivariable Cox Model

Fitted successfully using selected phenotype features and clinical subtype. The proportional-hazards test flagged assumption violations for some covariates — worth noting because a model running without errors isn't the same as a model meeting its assumptions.

### Zurich Loading and Matching

- 100,000 sampled cells; 92,642 with PhenoGraph labels
- 41 Zurich clusters, 33 common markers used
- 29 Basel-style phenotypes after matching; 347 metadata/abundance records

Cluster IDs are mapped to Basel-style labels using marker-profile similarity.

### Zurich Survival Skip

Usable Zurich survival rows: **0**. No non-missing overall-survival rows remained after merging metadata with phenotype abundance, so survival analysis was skipped. Zurich serves as phenotype and spatial validation only.

### Cross-Cohort Summary

| Metric | Result |
| --- | --- |
| Basel survival records | 376 |
| Zurich metadata/abundance records | 347 |
| Basel phenotypes tested | 71 |
| Zurich matched Basel-style phenotypes | 29 |
| Basel FDR-significant phenotypes | 0 / 71 |
| Zurich survival available | False |
| Basel mean spatial mixing | 0.669 |
| Zurich mean spatial mixing | 0.757 |

Basel handles survival reproduction. Zurich handles independent phenotype and spatial validation.

## Run The Notebook

Use `SurvivalAnalysis_github.ipynb` as the main deliverable. Open it in Google Colab, use an R runtime if available, and run the notebook from top to bottom. If Colab opens as Python, create an R notebook from https://colab.research.google.com/notebook#create=true&language=r, then load the notebook content there.

## Common Issues

### Rphenograph does not install

`Rphenograph` may not install from CRAN in Colab. The notebook installs it from GitHub using `JinmiaoChenLab/Rphenograph`.

### The subset has only about one row per cell

That means cells were sampled incorrectly by row. The original `SC_dat.csv` is long-format, so cells must be sampled by complete cell ID.

### Cox model warnings appear

Cox model warnings can happen with rare clusters or small event counts. Report the warnings, use FDR-adjusted results, and treat diagnostics as part of the result rather than an error to hide.

## Limitations

This is a methodological replication, not a pixel-perfect reproduction.

Not fully reproduced:

- Raw imaging mass cytometry acquisition.
- Raw image segmentation.
- Full original MATLAB/histoCAT workflow.
- Full-scale clustering of all cells from scratch.
- Exact paper figures and exact paper statistics.

That tradeoff is intentional. The notebook focuses on showing the scientific workflow in a form that can run in Colab.

## Glossary

### Imaging Mass Cytometry

A microscopy method that measures many protein markers in the same tissue while preserving cell locations.

### Marker

A protein measurement used to describe a cell.

### Cell Phenotype

A group of cells with similar marker-expression patterns.

### PhenoGraph

A clustering algorithm used in single-cell biology. It groups cells by similarity.

### t-SNE

A visualization method that turns high-dimensional cell measurements into a 2D map.

### Phenotype Abundance

The percentage of cells in a tissue core belonging to a given phenotype.

### Kaplan-Meier Curve

A survival plot showing estimated survival probability over time.

### Cox Proportional Hazards Model

A survival model used to estimate whether a variable is associated with higher or lower risk over time.

### Hazard Ratio

A risk estimate from a Cox model.

| Hazard ratio | Meaning |
| --- | --- |
| Greater than 1 | Higher risk |
| Equal to 1 | No clear risk difference |
| Less than 1 | Lower risk |

### FDR Adjustment

A correction for testing many hypotheses. It reduces false discoveries when many phenotypes are tested.

### Spatial Mixing Score

A score measuring how often a cell's nearest neighbors are different phenotypes. Higher values mean more mixing.

## Sources

| Source | Link |
| --- | --- |
| Jackson et al. Nature paper | https://doi.org/10.1038/s41586-019-1876-x |
| Nature article page | https://www.nature.com/articles/s41586-019-1876-x |
| Original code repository | https://github.com/BodenmillerGroup/SCPathology_publication |
| Zenodo main data record | https://doi.org/10.5281/zenodo.3518284 |
| Zenodo locations record | https://doi.org/10.5281/zenodo.4607374 |
