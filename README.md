<div align="center">

# 🧬 Single-Cell Pathology Breast Cancer Replication
**A Colab-friendly reproduction of Jackson et al. 2020, focused on phenotype abundance, spatial structure, and survival analysis.**

[![Notebook](https://img.shields.io/badge/Notebook-Google%20Colab-F9AB00?style=flat-square&logo=googlecolab)](SurvivalAnalysis_github.ipynb)
[![Language](https://img.shields.io/badge/Language-R-276DC3?style=flat-square&logo=r)](https://www.r-project.org/)
[![Paper](https://img.shields.io/badge/Paper-Nature-000000?style=flat-square)](https://doi.org/10.1038/s41586-019-1876-x)
[![Data](https://img.shields.io/badge/Data-Zenodo-1682D4?style=flat-square)](https://doi.org/10.5281/zenodo.3518284)

[Overview](#overview) · [What This Reproduces](#what-this-reproduces) · [Results](#results) · [Run The Notebook](#run-the-notebook) · [Limitations](#limitations)

</div>

---

## Overview

This project is a methodological replication of:

Hartland W. Jackson, Jana R. Fischer, R. T. Zanotelli, et al. "The single-cell pathology landscape of breast cancer." Nature 578, 615-620 (2020). DOI: https://doi.org/10.1038/s41586-019-1876-x

The original study used imaging mass cytometry to measure many protein markers in breast cancer tissue at single-cell resolution. Instead of treating a tumor as one bulk sample, it mapped individual cells, grouped them into phenotypes, measured where they sit in tissue, and connected those patterns to clinical outcome.

This notebook does not rerun the full image-processing pipeline. It reproduces the main analysis logic in a smaller, Colab-friendly form using public data and the paper's released labels where full recomputation would be too heavy.

## What This Reproduces

The project follows the core workflow of the paper: public single-cell marker data are loaded, complete cells are sampled, marker rows are converted into cell-by-marker tables, published PhenoGraph labels are merged, and phenotype abundance is summarized at the tissue-core level. The notebook then runs survival analysis for Basel and spatial-neighborhood analysis for both Basel and Zurich.

It also includes a small independent PhenoGraph demonstration on sampled cells. That demo is intentionally smaller than the paper's full clustering run, but it shows how marker-expression profiles become cell phenotypes.

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
Phenotype labels and abundance tables
        |
        v
Survival, spatial mixing, and cross-cohort comparison
```

## Repository Files

| File | Purpose |
| --- | --- |
| `SurvivalAnalysis_github.ipynb` | Main cleaned notebook with outputs preserved. |
| `README.md` | Project summary, results, and run guide. |

## Results

The final Colab run analyzed 100,000 complete sampled cells from Basel and 100,000 complete sampled cells from Zurich. Basel was the main survival cohort, while Zurich was used for independent phenotype and spatial validation because usable merged survival rows were not available.

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

The survival screen tested all 71 Basel phenotypes against overall survival. Some raw associations appeared for clusters such as 43, 15, 68, 34, and 54, but none survived FDR correction in this downsampled reproduction. The multivariable Cox model fitted successfully, although proportional-hazards diagnostics showed some violations, so those results should be interpreted cautiously.

Spatially, both cohorts produced interpretable cell-neighborhood summaries from X/Y coordinates. Zurich showed a slightly higher mean spatial mixing score in the sampled cores, but this should be treated as a demonstration of the workflow rather than a definitive biological claim.

## Visual Outputs

The notebook is designed as a visual learning path. It includes a data funnel, marker distribution plots, a PhenoGraph marker heatmap, t-SNE cell landscape, phenotype abundance plot, Kaplan-Meier curves, hazard-ratio forest plot, spatial cell map, and a Basel-vs-Zurich cohort dashboard.

These figures are meant to show how raw single-cell tables become interpretable biological and clinical summaries.

## Run The Notebook

Use `SurvivalAnalysis_github.ipynb` as the main deliverable. Open it in Google Colab, use an R runtime if available, and run the notebook from top to bottom. If Colab opens as Python, create an R notebook from https://colab.research.google.com/notebook#create=true&language=r, then load the notebook content there.

## Common Issues

`Rphenograph` may not install from CRAN in Colab. The notebook installs it from GitHub using `JinmiaoChenLab/Rphenograph`.

If a subset has only about one row per cell, the data were sampled incorrectly by row. The original `SC_dat.csv` is long-format, so cells must be sampled by complete cell ID.

If Zurich survival has zero usable rows, that matches this run. Zurich still works for phenotype abundance, cluster matching, and spatial validation.

Cox model warnings can happen with rare clusters or small event counts. Report the warnings, use FDR-adjusted results, and treat diagnostics as part of the result rather than an error to hide.

## Limitations

This is a methodological replication, not a pixel-perfect reproduction. It does not reproduce raw imaging mass cytometry acquisition, raw image segmentation, the full original MATLAB/histoCAT workflow, full-scale clustering of all cells from scratch, or the exact paper figures and statistics.

That tradeoff is intentional. The notebook focuses on showing the scientific workflow in a form that can run in Colab: load public data, preserve complete cells, use published phenotype labels, demonstrate clustering, build phenotype abundance features, run Basel survival analysis, and compare Basel with Zurich spatially.

## Sources

| Source | Link |
| --- | --- |
| Jackson et al. Nature paper | https://doi.org/10.1038/s41586-019-1876-x |
| Nature article page | https://www.nature.com/articles/s41586-019-1876-x |
| Original code repository | https://github.com/BodenmillerGroup/SCPathology_publication |
| Zenodo main data record | https://doi.org/10.5281/zenodo.3518284 |
| Zenodo locations record | https://doi.org/10.5281/zenodo.4607374 |
