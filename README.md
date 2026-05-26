# Replicating Jackson et al. 2020 in Google Colab

<div align="center">

**Single-cell pathology of breast cancer, explained and reproduced in principle**

[Paper](https://doi.org/10.1038/s41586-019-1876-x) . [Nature page](https://www.nature.com/articles/s41586-019-1876-x) . [Original code](https://github.com/BodenmillerGroup/SCPathology_publication) . [Data](https://doi.org/10.5281/zenodo.3518284)

</div>

## Overview

This project reproduces the main analysis logic from:

Hartland W. Jackson, Jana R. Fischer, Vito R. T. Zanotelli, et al. "The single-cell pathology landscape of breast cancer." Nature 578, 615-620 (2020). DOI: https://doi.org/10.1038/s41586-019-1876-x

The original paper used imaging mass cytometry to study breast cancer tissue at single-cell resolution. Instead of only asking whether a tumor is ER-positive, PR-positive, or HER2-positive, the paper asks a richer question:

Can we measure many proteins in every cell, identify different cell phenotypes, see where those cells sit in the tissue, and connect those patterns to patient survival?

Our Google Colab notebook does not rerun every heavy image-processing step from the paper. Instead, it gives a Colab-friendly educational reproduction of the core workflow:

1. Load public single-cell pathology data.
2. Rebuild complete-cell subsets that fit in Colab.
3. Convert long-format cell marker data into a cell-by-marker table.
4. Use the paper's published PhenoGraph phenotype labels.
5. Run a smaller independent PhenoGraph demo.
6. Visualize cell landscapes and phenotype abundance.
7. Run Kaplan-Meier and Cox survival analysis for Basel.
8. Demonstrate spatial neighborhood mixing.
9. Load Zurich as an independent validation cohort for phenotype and spatial analysis.

## Repository Files

| File | Purpose |
| --- | --- |
| `jackson2020.pdf` | Local copy of the Nature paper. |
| `SurvivalAnalysis.ipynb` | Raw final notebook downloaded from Colab, with outputs preserved. |
| `SurvivalAnalysis_github.ipynb` | Clean GitHub-ready notebook: outputs preserved, debug cells removed, widget/Colab metadata stripped. |
| `README.md` | This explanation and reproduction guide. |
| `refrence README.md` | Style reference used for this final README. |

## The Paper In Plain English

Think of a tumor tissue slide as a crowded city.

Traditional pathology gives useful but coarse information, like whether the city has many factories, houses, or hospitals. Jackson et al. built a much more detailed city map. In their map:

- Each building is a single cell.
- Each building has many labels, such as protein markers.
- Buildings with similar labels are grouped into neighborhoods, or cell phenotypes.
- The map keeps track of where every building is located.
- The final question is whether certain city layouts are linked to better or worse patient outcomes.

The paper used imaging mass cytometry, or IMC. IMC is high-dimensional microscopy: it measures many protein markers at once in tissue while preserving spatial coordinates. The study analyzed hundreds of breast cancer tissue images and showed that single-cell spatial organization can reveal clinically meaningful tumor subgroups.

## What The Paper Did

The paper's analysis pipeline can be summarized like this:

```text
Breast cancer tissue
        |
        v
Imaging mass cytometry images
        |
        v
Single-cell segmentation
        |
        v
Marker measurements per cell + X/Y cell locations
        |
        v
PhenoGraph clustering into cell phenotypes
        |
        v
Phenotype abundance and spatial neighborhoods
        |
        v
Patient groups, survival analysis, and biological interpretation
```

Important paper-level outputs include:

| Paper output | Meaning | What our notebook does |
| --- | --- | --- |
| t-SNE cell landscape | A 2D map where similar cells appear near each other. | Uses the paper's saved t-SNE coordinates for matched Basel cells. |
| PhenoGraph phenotypes | Clusters cells by marker-expression similarity. | Uses paper labels downstream and runs a 10k-cell demo from scratch. |
| Phenotype marker profiles | Shows which markers define each cell phenotype. | Computes average marker expression for demo clusters. |
| Phenotype abundance | Counts how much of each phenotype exists per tissue core. | Builds abundance tables for Basel and Zurich. |
| Survival analysis | Tests whether cell patterns relate to patient outcome. | Runs Kaplan-Meier and Cox models for Basel. |
| Spatial neighborhoods | Tests which cell types are mixed or separated in tissue. | Computes simplified k-nearest-neighbor spatial mixing scores. |
| Cross-cohort validation | Checks whether findings transfer to another cohort. | Loads Zurich and maps Zurich clusters to Basel-style labels. |

## What We Actually Reproduced

This is the final outcome from our working Colab run.

| Analysis step | Result from our run | Interpretation |
| --- | --- | --- |
| Basel complete-cell subset | 100,000 cells | Small enough for Colab, but each sampled cell keeps all marker rows. |
| Basel long-format rows | 6,400,000 rows | Confirms complete-cell sampling worked. |
| Basel wide cell table | 100,000 x 67 | One row per cell, many marker/metadata columns. |
| Basel master table | 100,000 x 73 | Marker expression, coordinates, paper labels, and IDs merged. |
| Basel paper phenotypes | 71 clusters | Published PhenoGraph labels used for downstream analysis. |
| Basel t-SNE cells matched | 19,838 cells | Enough to show the paper's cell-landscape idea. |
| Independent PhenoGraph demo | 10,000 cells, 33 marker columns | Demonstrates how clustering works without rerunning the full paper pipeline. |
| Demo PhenoGraph clusters | 30 clusters | A simplified clustering result from the subset. |
| Demo vs paper ARI | 0.131 | Low agreement is expected because this is a small simplified demo, not the full original pipeline. |
| Basel phenotype abundance | 381 cores x 71 phenotypes | Converts cell labels into patient/core-level features. |
| Basel metadata overlap | 376 records | These records were usable for survival analysis. |
| Basel survival events | 102 deaths | Outcome data used in Cox and Kaplan-Meier analysis. |
| Basel survival screen | 71 phenotypes tested | Tests each phenotype abundance against overall survival. |
| Basel FDR-significant phenotypes | 0 / 71 | No phenotype survived multiple-testing correction in this downsampled run. |
| Basel top raw associations | Clusters 43, 15, 68, 34, 54 | Interesting unadjusted signals, but not FDR-significant. |
| Basel multivariable Cox model | Fitted successfully | Includes common abundant clusters and subtype. |
| Cox diagnostics | Some PH violations | Must be reported; model assumptions were not perfect. |
| Basel spatial demo | 5 cores | Shows how spatial mixing can be computed from X/Y coordinates. |
| Mean Basel spatial mixing | 0.669 | Moderate mixing in the sampled cores. |
| Zurich complete-cell subset | 100,000 cells | Independent cohort loaded for validation. |
| Zurich PhenoGraph labels matched | 92,642 cells | Most Zurich sampled cells had paper labels. |
| Zurich cluster matching | 41 Zurich clusters mapped to Basel-style labels | Uses marker-profile similarity. |
| Zurich matched Basel-style phenotypes | 29 phenotypes | Subset of Basel-style phenotypes represented in sampled Zurich cells. |
| Zurich metadata/abundance overlap | 347 records | Usable for phenotype and spatial summaries. |
| Zurich survival rows | 0 usable rows | Survival analysis skipped because merged Zurich survival fields were missing/non-usable. |
| Mean Zurich spatial mixing | 0.757 | Zurich sampled cores were slightly more mixed than Basel sampled cores. |

## Final Cross-Cohort Summary

| Metric | Basel | Zurich |
| --- | ---: | ---: |
| Survival/metadata records used | 376 | 347 metadata/abundance records |
| Phenotypes tested for survival | 71 | Not tested |
| Matched Basel-style phenotypes | 71 | 29 |
| FDR-significant survival phenotypes | 0 / 71 | N/A |
| Spatial demo cores | 5 | 5 |
| Mean spatial mixing score | 0.669 | 0.757 |
| Survival available | Yes | No usable merged survival rows |

Bottom line: Basel is the main cohort for survival reproduction. Zurich still matters because it shows that the phenotype and spatial workflow can be applied to an independent TMA cohort.

## Key Visual Outputs

The notebook should be read as a visual learning path, not only as a computation script.

### 1. Data Funnel

Shows how many rows/cells survive each step:

```text
Raw long-format single-cell data
        |
        v
Complete-cell subset
        |
        v
Wide cell-by-marker matrix
        |
        v
Master table with spatial coordinates and paper cluster labels
```

Why it matters: beginners can see why the notebook samples complete cells instead of random rows. Random row sampling breaks cells because the original table stores one cell across many marker rows.

### 2. Marker Distributions

A density or histogram plot of selected markers shows that cells have different protein-expression patterns. This makes the clustering step less abstract.

### 3. t-SNE Cell Landscape

Each point is a cell. Similar cells appear near each other. Coloring by PhenoGraph cluster recreates the idea of the paper's cell atlas.

### 4. Cluster Marker Heatmap

Rows are cell clusters. Columns are markers. Bright or high values show which markers define a cluster.

### 5. Phenotype Abundance Bar Plot

Shows how common each cell phenotype is across sampled Basel cells. This is the bridge between single-cell biology and patient-level survival analysis.

### 6. Kaplan-Meier Curves

Shows survival over time for patients with high vs low abundance of selected phenotypes.

### 7. Hazard-Ratio Forest Plot

Summarizes Cox model results. Hazard ratio greater than 1 means higher risk; less than 1 means lower risk.

### 8. Spatial Scatter Plot

Plots real X/Y cell positions for a tissue core, colored by phenotype. This is the easiest way to see that the data are spatial, not just tabular.

### 9. Basel vs Zurich Comparison Plot

Compares phenotype counts, spatial mixing, and availability of survival analysis across cohorts.

## Output-By-Output Guide

This section explains the notebook outputs in the order a beginner sees them. The goal is not just to show that code ran, but to explain what each result teaches.

### Output 1: Package Installation

What you see:

- R installs and loads packages such as `data.table`, `ggplot2`, `survival`, `survminer`, `RANN`, and `Rphenograph`.
- `Rphenograph` installs from GitHub instead of CRAN.
- Final message: dependencies loaded successfully.

What it means:

The notebook is preparing tools for fast tables, plotting, clustering, nearest-neighbor spatial analysis, and survival modeling.

Why it matters:

Without these packages, Colab cannot reproduce the paper's core workflow. The important fix was installing `Rphenograph` from `JinmiaoChenLab/Rphenograph`, because CRAN did not provide a compatible version for the Colab R runtime.

Check yourself:

If Cell 1 says `Rphenograph` is missing, the rest of the phenotype demo cannot run.

### Output 2: Configuration

What you see:

- Dataset paths for Basel and Zurich.
- `COHORT = "both"`.
- `DOWNSAMPLE = 100000L`.
- Zenodo download links.

What it means:

The notebook is set to analyze both cohorts using 100,000 complete cells per cohort.

Why it matters:

The original dataset is too large for a casual Colab run. Downsampling makes the workflow teachable and runnable while preserving the structure of the analysis.

### Output 3: Complete-Cell Subsetting

What you see:

- Basel subset created from complete cell IDs.
- Zurich subset created from complete cell IDs.
- Basel long-format subset has 6,400,000 rows for 100,000 unique cells.
- Zurich long-format subset has 6,800,000 rows for 100,000 unique cells.

What it means:

The raw single-cell table is long-format: one cell appears across many marker rows. We fixed the early mistake of sampling random rows by instead sampling whole cell IDs.

Why it matters:

Random row sampling breaks the biology. A cell with only one marker row is not a usable cell. Complete-cell sampling keeps the full marker profile for each selected cell.

### Output 4: Basel Master Table

What you see:

- Basel metadata rows: 376.
- Long-format cell rows: 6,400,000.
- Unique Basel cells: 100,000.
- Wide table: 100,000 x 67.
- Spatial locations matched: 100,000.
- Paper PhenoGraph labels matched: 100,000.
- t-SNE cells matched: 19,838.
- Final Basel master table: 100,000 x 73.

What it means:

The notebook successfully merged four layers:

- Marker measurements.
- Cell IDs and tissue core IDs.
- Spatial X/Y coordinates.
- Paper-provided PhenoGraph phenotype labels.

Why it matters:

This is the central table. Every later analysis depends on it.

### Output 5: Data Funnel Plot

What you see:

A horizontal bar chart showing the flow from raw marker rows to unique cells, wide matrix, spatial matches, cluster labels, and t-SNE matches.

What it means:

This plot turns the data-cleaning pipeline into a visual story.

Why it matters:

Beginners can see that millions of marker rows eventually become a smaller but meaningful table of complete cells.

### Output 6: Marker Distribution Plots

What you see:

Histograms for high-variance marker columns.

What it means:

Cells do not all express markers equally. Some cells are high for a marker, some are low, and some markers vary strongly across the sample.

Why it matters:

Clustering only makes sense if cells differ in marker expression. This output visually proves that there is biological variation for PhenoGraph to learn from.

### Output 7: Independent PhenoGraph Demo

What you see:

- 10,000 cells clustered.
- 33 protein/marker columns used.
- 30 demo clusters found.
- Cluster marker profiles printed.

What it means:

The notebook independently demonstrates the same kind of clustering method used in the paper, but on a smaller Colab-friendly sample.

Why it matters:

This proves we understand the method, not just how to load the paper's saved labels.

### Output 8: Demo Cluster Heatmap

What you see:

A heatmap where rows are demo PhenoGraph clusters and columns are markers.

What it means:

Each cluster has a marker fingerprint. Some clusters are high for epithelial markers, some for immune/stromal/proliferation-related markers, depending on the marker panel.

Why it matters:

This is how abstract cluster numbers become interpretable cell phenotypes.

### Output 9: Demo vs Paper Cluster Agreement

What you see:

- Cross-tabulation between demo clusters and paper clusters.
- Adjusted Rand Index: 0.131.

What it means:

The demo clustering partially overlaps with paper labels, but it does not reproduce the full paper clustering.

Why it matters:

This is expected. The notebook clusters only 10,000 sampled cells with a simplified workflow. The paper used a full-scale, carefully processed dataset. The low ARI should be reported honestly as a limitation.

### Output 10: t-SNE Cell Landscape

What you see:

A 2D scatter plot of matched Basel cells, colored by PhenoGraph phenotype.

What it means:

Cells with similar marker patterns appear near each other. Separated regions often correspond to different cell states or phenotypes.

Why it matters:

This recreates the idea of the paper's Figure 1 cell landscape.

### Output 11: Basel Phenotype Abundance Matrix

What you see:

- Phenotype abundance matrix: 381 cores x 71 phenotypes.
- Metadata cores: 376.
- Intersection: 376.

What it means:

Each tissue core is summarized by the percentage of its cells in each phenotype.

Why it matters:

This is the bridge from single-cell biology to patient-level clinical analysis.

### Output 12: Most Common Phenotype Bar Plot

What you see:

A ranked bar chart of the most common Basel cell phenotypes.

What it means:

Some phenotypes are common across many tissue cores, while others are rare.

Why it matters:

Rare phenotypes can create unstable survival estimates. This helps explain why some Cox warnings appear later.

### Output 13: Basel Survival Screening

What you see:

- Time column: `OSmonth`.
- Status column: `Patientstatus`.
- Patients in survival analysis: 376.
- Events/deaths: 102.
- Phenotypes tested: 71.
- Top raw associations include clusters 43, 15, 68, 34, and 54.
- FDR-significant phenotypes: 0 / 71.

What it means:

For each phenotype, patients were split into high- and low-abundance groups and tested for survival association.

Why it matters:

Raw p-values can look interesting, but after correcting for 71 tests, no phenotype remained statistically significant in this downsampled reproduction.

### Output 14: Kaplan-Meier Curves

What you see:

Several survival curves comparing high vs low phenotype abundance groups.

What it means:

Each curve shows survival probability over time. If two curves separate strongly, that phenotype may be associated with outcome.

Why it matters:

This is the most intuitive survival output for non-technical audiences.

### Output 15: Hazard-Ratio Forest Plot

What you see:

A forest plot of log hazard ratios and confidence intervals for top phenotypes.

What it means:

- Points to the right suggest higher risk.
- Points to the left suggest lower risk.
- Wide bars mean uncertainty.
- Bars crossing zero mean the result is not clearly different from no effect.

Why it matters:

This gives a compact statistical summary of survival associations.

### Output 16: Multivariable Cox Model

What you see:

A Cox model including selected phenotype abundance variables and clinical subtype.

What it means:

The model asks whether phenotype abundance is associated with survival while also considering available clinical information.

Why it matters:

The paper's clinical claim is not just that cell types exist. The important question is whether they add clinically meaningful information.

Important warning:

The proportional hazards test showed violations for some covariates. This does not make the notebook useless, but it means the model diagnostics must be reported.

### Output 17: Basel Spatial Neighborhood Summary

What you see:

- Five Basel tissue cores analyzed.
- Each core has cell counts, unique clusters, dominant cluster, and spatial mixing score.
- Mean Basel spatial mixing score: 0.669.

What it means:

For each cell, the notebook looks at nearby cells and asks whether neighbors tend to be the same phenotype or different phenotypes.

Why it matters:

The paper is about spatial pathology, not only cell counts. This output introduces tissue organization.

### Output 18: Spatial Cell Map

What you see:

A scatter plot of real X/Y cell coordinates for one Basel tissue core, colored by phenotype.

What it means:

Each dot is a real cell location in tissue.

Why it matters:

This is often the easiest plot for beginners: it shows that the dataset is literally a map of cells in tissue.

### Output 19: Validation Summary

What you see:

A summary table comparing the paper and our notebook.

What it means:

It lists what was matched, what was simplified, and what was only demonstrated in principle.

Why it matters:

This is the honest scientific framing of the project. It prevents overclaiming.

### Output 20: Zurich Loading

What you see:

- Zurich metadata rows: 359.
- Zurich sampled cells: 100,000.
- Zurich wide table: 100,000 x 71.
- Zurich spatial coordinates matched: 100,000.
- Zurich PhenoGraph labels matched: 92,642.

What it means:

The independent Zurich cohort was successfully loaded and merged with spatial and phenotype data.

Why it matters:

An independent cohort makes the project stronger, even though Zurich survival could not be used.

### Output 21: Zurich To Basel Cluster Matching

What you see:

- 41 Zurich clusters.
- 71 Basel clusters available.
- 33 common markers used.
- Zurich clusters mapped to Basel-style labels.
- 29 Basel-style phenotypes represented in Zurich.

What it means:

Zurich and Basel cluster numbers are not automatically the same. The notebook compares marker-expression profiles and maps Zurich clusters to the closest Basel-style phenotype.

Why it matters:

This makes cross-cohort comparison possible in principle.

### Output 22: Zurich Analysis

What you see:

- Zurich phenotype abundance matrix: 353 cores x 29 matched phenotypes.
- Metadata/abundance overlap: 347.
- Usable Zurich survival rows: 0.
- Zurich survival skipped.
- Zurich spatial demo: 5 cores.
- Mean Zurich spatial mixing score: 0.757.

What it means:

Zurich works for phenotype and spatial analysis, but not survival analysis in this merged public subset.

Why it matters:

This is a good example of scientific restraint: we do not force a survival model when the data are not usable.

### Output 23: Basel vs Zurich Cross-Cohort Comparison

What you see:

| Metric | Result |
| --- | --- |
| Basel survival records | 376 |
| Zurich metadata/abundance records | 347 |
| Basel phenotypes tested | 71 |
| Zurich matched phenotypes | 29 |
| Basel FDR-significant phenotypes | 0 / 71 |
| Zurich survival available | False |
| Basel mean spatial mixing | 0.669 |
| Zurich mean spatial mixing | 0.757 |

What it means:

Basel supports the survival reproduction. Zurich supports independent phenotype and spatial validation.

Why it matters:

This gives the final project conclusion in one table.

### Output 24: Cohort Contribution Dashboard

What you see:

A tile plot showing:

- Basel: phenotypes available, spatial available, survival available.
- Zurich: phenotypes available, spatial available, no usable overall survival data.

What it means:

Each cohort contributes a different layer of evidence.

Why it matters:

This is a clean final teaching graphic for presentations and GitHub readers.

## How To Run The Notebook

Use `SurvivalAnalysis_github.ipynb` as the main GitHub deliverable. It keeps the important outputs but removes Colab-specific metadata and debugging cells.

1. Open Google Colab: https://colab.research.google.com/
2. Upload or open `SurvivalAnalysis_github.ipynb`.
3. Use an R runtime if available.
4. If Colab opens as Python, create an R notebook from: https://colab.research.google.com/notebook#create=true&language=r
5. Choose a high-RAM runtime if available.
6. Run cells from top to bottom.
7. If a cell fails because of package installation or temporary Colab state, restart runtime and rerun from Cell 1.

Recommended configuration:

```r
CONFIG <- list(
  COHORT = "both",
  DOWNSAMPLE = 100000L,
  SEED = 42L
)
```

For a faster demonstration, use Basel only:

```r
CONFIG$COHORT <- "Basel"
CONFIG$DOWNSAMPLE <- 50000L
```

GPU is not required. Disk space and RAM matter more.

## Notebook Section Guide

| Section | What happens | Beginner explanation |
| --- | --- | --- |
| Setup | Install packages | Gives Colab the R tools needed for tables, plots, survival models, spatial analysis, and PhenoGraph. |
| Configuration | Set paths and parameters | Tells the notebook where files live and how many cells to sample. |
| Data acquisition | Download and subset data | Creates complete-cell subsets for Basel and Zurich. |
| Basel merge | Load Basel markers, labels, and coordinates | Builds the main Basel single-cell table. |
| Visual checkpoint | Data funnel and marker histograms | Helps beginners see how raw rows become usable cells and why markers carry signal. |
| PhenoGraph demo | Cluster 10,000 cells | Shows how cells can be clustered by marker expression. |
| Cluster validation | Compare demo clusters to paper clusters | Uses ARI to quantify agreement and explain why exact matching is not expected. |
| Visual checkpoint | Cluster marker heatmap | Turns cluster numbers into marker fingerprints. |
| t-SNE | Plot the cell landscape | Shows the paper's cell-atlas idea using saved t-SNE coordinates. |
| Abundance | Build phenotype abundance matrix | Converts cell-level labels into patient/core-level percentages. |
| Visual checkpoint | Phenotype abundance bar chart | Shows which phenotypes are common and which are rare. |
| Survival | Run Kaplan-Meier and Cox screening | Tests each phenotype against overall survival in Basel. |
| Cox model | Fit multivariable model and hazard plot | Summarizes risk direction and model diagnostics. |
| Spatial analysis | Analyze nearest-neighbor mixing | Measures how mixed or separated phenotypes are in tissue. |
| Visual checkpoint | Spatial cell map | Shows real tissue cell coordinates colored by phenotype. |
| Basel summary | Validation table | Summarizes what was reproduced and what remains limited. |
| Zurich loading | Load independent cohort | Repeats the data-loading logic for Zurich. |
| Zurich matching | Map Zurich clusters to Basel-style labels | Allows cross-cohort phenotype comparison despite different cluster IDs. |
| Zurich analysis | Phenotype and spatial validation | Uses Zurich for validation and skips survival because usable rows are missing. |
| Cross-cohort summary | Basel vs Zurich comparison | Compares what each cohort contributes. |
| Final dashboard | Cohort contribution plot | Gives a clean final visual summary for GitHub and presentations. |

## Important Limitations

This is a methodological replication, not a pixel-perfect reproduction.

Not fully reproduced:

- Raw IMC image acquisition.
- Raw image segmentation.
- Full original MATLAB/histoCAT image-processing pipeline.
- Full-scale clustering of all cells from scratch.
- Exact paper figures and exact reported statistical findings.

Why this is acceptable for this project:

- The goal is to demonstrate understanding and reproduce the core analysis logic.
- The public paper outputs are used where full recomputation would be too heavy for Colab.
- The notebook clearly separates full-paper labels from our smaller independent demo.
- Every limitation is reported rather than hidden.

## How To Explain The Results

Use this short explanation in a viva or presentation:

The paper shows that breast cancer tissue can be studied as a spatial single-cell ecosystem. Our notebook reproduces that idea in Colab. We load public single-cell data, keep complete sampled cells, use the paper's published phenotype labels, create phenotype abundance features per tissue core, test those features against survival in Basel, and demonstrate spatial mixing from X/Y cell coordinates. We also load Zurich as an independent cohort and map its clusters to Basel-style phenotypes. The reproduction is intentionally downsampled, so exact paper-level results are not expected, but the scientific workflow is reproduced in principle.

## Common Problems And Fixes

### Rphenograph does not install

`Rphenograph` is not reliably available from CRAN in Colab. Install it from GitHub:

```r
remotes::install_github(
  "JinmiaoChenLab/Rphenograph",
  dependencies = TRUE,
  upgrade = "never"
)
```

### Spatial location file is not found

In our run, locations were found at:

```text
Dataset_SC/Basel_SC_locations.csv
Dataset_SC/Zurich_SC_locations.csv
```

If a cell expects `Dataset_SC/Spatial_Locations/...`, update the path or use a file-finder helper.

### Subset has only about one row per cell

That means the data were sampled incorrectly by row. The original `SC_dat.csv` is long-format, so one cell appears across many marker rows. Rebuild the subset by sampling complete cell IDs.

### Zurich survival has zero usable rows

This happened in our run. It is not a failure of the whole notebook. Zurich still works for phenotype abundance, cluster matching, and spatial validation. Basel remains the survival cohort.

### Cox model warnings appear

Cox warnings can happen when a cluster is rare or when a group has very few events. Report the warning and use FDR-adjusted results and diagnostics.

## Glossary

### Imaging Mass Cytometry

A microscopy method that measures many protein markers in the same tissue section while preserving where cells are located.

### Marker

A protein measurement used to describe a cell. Examples include immune, tumor, stromal, proliferation, or hormone-receptor markers.

### Cell Phenotype

A group of cells with similar marker-expression patterns. In this project, phenotypes come from PhenoGraph clustering.

### PhenoGraph

A clustering algorithm often used in single-cell biology. It builds a graph of similar cells and finds communities in that graph.

### t-SNE

A visualization method that places high-dimensional cells onto a 2D map. It is used for seeing broad structure, not for exact distance measurement.

### Phenotype Abundance

The percentage of cells in a tissue core that belong to a given phenotype.

### Kaplan-Meier Curve

A survival plot showing the estimated probability of survival over time.

### Cox Proportional Hazards Model

A survival model that estimates whether a variable is associated with higher or lower risk over time.

### Hazard Ratio

A risk ratio from a Cox model.

| Hazard ratio | Meaning |
| --- | --- |
| Greater than 1 | Higher risk |
| Equal to 1 | No clear risk difference |
| Less than 1 | Lower risk |

### FDR Adjustment

A correction for testing many hypotheses. In this notebook, raw p-values may look interesting, but FDR-adjusted significance is the safer result to report.

### Spatial Mixing Score

A simplified score measuring how often a cell's nearest neighbors belong to different phenotypes. Higher values mean more mixing between cell types.

## Sources

- Jackson et al. Nature paper: https://doi.org/10.1038/s41586-019-1876-x
- Nature article page: https://www.nature.com/articles/s41586-019-1876-x
- Original code repository: https://github.com/BodenmillerGroup/SCPathology_publication
- Zenodo main data record: https://doi.org/10.5281/zenodo.3518284
- Zenodo additional locations record: https://doi.org/10.5281/zenodo.4607374
