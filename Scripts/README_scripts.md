[README_SCRIPTS_EN.md](https://github.com/user-attachments/files/27899710/README_SCRIPTS_EN.md)

# Scripts: Reproducible Analysis Code for Landscape Composition and Configuration as Drivers of Effective Habitat Area

This directory contains the complete R source code for replication of all statistical, multivariate, and diagnostic analyses presented in the manuscript submitted to *Landscape Ecology*.

## Overview

Scripts were developed in **R 4.5.2** with emphasis on reproducibility, transparency, and auditability. The analytical workflow encompasses:

- Extraction of landscape metrics from land-cover rasters (Fragstats-like via `landscapemetrics`)
- Multivariate analysis of habitat composition structure (NMDS, PERMANOVA, betadisper)
- Generalized Linear Mixed Models (GLMM) with Gamma family and log link
- Multimodel inference based on corrected Akaike Information Criterion (AICc)
- Residual diagnostics via simulation-based DHARMa framework

All scripts incorporate best practices in computational reproducibility: fixed random seeds, relative paths via `here::here()`, inline documentation, and version control integration.

## Script Descriptions

### 1. **Calculos_Landscape_Metrics.R**

Automated pipeline for extraction of landscape metrics from multi-species land-cover rasters (MapBiomas Collection 10.1).

**Core Workflow:**

- **Input raster processing:** Read GeoTIFF files for each sampling unit × species combination
- **Land-use reclassification:** Agricultural classes (code 4) aggregated into agropecuaria class (code 3) per LULC conceptualization
- **Metric calculation:** Class-level (`landscapemetrics::calculate_lsm()`) computation of:
  - Class Area (CA, hectares)
  - Percentage of Landscape (PLAND, %)
  - Number of Patches (NP)
  - Patch Density (PD, patches per 100 hectares)
  - Euclidean Nearest Neighbor Distance (ENN_MN, meters)
  - Mean Fractal Dimension (FRAC_MN)
  - Largest Patch Index (LPI, %)
  - Edge Density (ED, meters per hectare)
- **Proximity index (PROX_MN) integration:** Extracted from FRAGSTATS Patch output files via automated parsing and class-level aggregation
- **Data consolidation:** Merge of landscape metrics with biological (genus, species, diet, locomotion), environmental (elevation), and socioeconomic (human population density) attributes
- **Output:** Single rectangular matrix (`Data_Raw_GLM_NMDS_Final.csv`) suitable for univariate and multivariate statistical modeling

**R Dependencies:**
```r
terra              # raster manipulation and geospatial I/O
landscapemetrics  # landscape metric computation
tidyverse         # data wrangling (dplyr, tidyr, ggplot2)
here              # reproducible relative paths
```

**Input Files:**
- GeoTIFF rasters: `/Dados/Rasters/UA_RASTER/{UA_ID}_{SPECIES_CODE}.tif`
- FRAGSTATS output: `/Dados/FRAGSTATS_RESULT/*Patch.csv`
- Auxiliary table: `/Dados/Processados/Caracteristicas_Social_EnviromentVariables.CSV`

**Output File:**
- `Data_Raw_GLM_NMDS_Final.csv` (input for downstream analyses)

---

### 2. **Script_LANDSCAPE_ECOLOGY_REVISED_v3_ColorPalettes.R**

Comprehensive analytical pipeline comprising collinearity diagnostics, multivariate community structure analyses, univariate generalized linear mixed models, and publication-quality visualizations.

#### 2.1 Collinearity Diagnostics

- **Pearson correlation matrix:** Computed across all landscape metrics and the response variable (EHA in hectares)
- **Variance Inflation Factor (VIF):** Calculated under simple linear regression framework; threshold for exclusion set at VIF > 5
- **Output:** `Table_S_Collinearity_VIF.csv`

**Action Taken:** Variables with VIF ≥ 5 were excluded from GLMM candidate sets. Specifically, Largest Patch Index (LPI) exhibited severe collinearity with PLAND (VIF = 53.12 and 48.79, respectively) and was accordingly excluded from univariate model selection. LPI was retained in the environmental vector fitting (envfit) analysis because its ecological interpretation (dominance of a single core remnant) is structurally distinct from the proportional cover captured by PLAND.

#### 2.2 Multivariate Analyses

**Non-metric Multidimensional Scaling (NMDS):**
- Distance metric: Bray-Curtis
- Convergence criterion: stress < 0.20 (adequacy threshold)
- Ordination space: 2 dimensions
- Species/landscape-metric vectors scaled prior to analysis

**Permutational Multivariate Analysis of Variance (PERMANOVA):**
- Global tests of compositional differences among levels of four grouping factors:
  - Diet (Carnivore, Frugivore/Folivore, Herbivore/Frugivore, Frugivore/Omnivore)
  - Locomotion (Arboreal, Scansorial, Terrestrial)
  - Genus (8 genera)
  - Species (9 species)
- Permutations: 9,999
- Distance metric: Bray-Curtis
- Multiple testing correction: False Discovery Rate (Benjamini-Hochberg)
- Output: `Table_S_PERMANOVA_Global_Bray.csv`

**Pairwise PERMANOVA:**
- Exhaustive pairwise comparisons among factor levels
- Permutations: 9,999 per comparison
- Correction: FDR
- Outputs: `PERMANOVA_Bray_Pairwise_[Factor].csv` (Diet, Locomotion, Genus, Species)

**Betadisper (Test of Multivariate Homogeneity of Dispersions):**
- Assesses whether groups differ in multivariate spread (β-diversity)
- Significance threshold: α = 0.05
- Interpretation: Significant betadisper indicates heterogeneous group dispersions; PERMANOVA centroid tests must be interpreted cautiously in this case
- Output: `Table_S_Betadisper_Bray_Results.csv`

**Environmental Vector Fitting (envfit):**
- Linear regression of landscape metrics onto NMDS ordination axes
- Reported R² per metric (coefficient of determination)
- Permutation significance testing (9,999 iterations)
- Visualization threshold: R² > 0.25 and p < 0.05
- Output: `Envfit_Configuration_Bray_Results.csv`

#### 2.3 Univariate Generalized Linear Mixed Models (GLMM)

**Model Specification:**

For each of the nine focal taxa:
- **Response:** EHA_ha (Effective Habitat Area, hectares; see Section 2.3.4)
- **Family:** Gamma with log link (appropriate for continuous, strictly positive response with right-skewed distribution)
- **Fixed effects:** Candidate landscape metrics (PLAND, PD, NP, ED, FRAC_MN, PROX_MN; LPI excluded due to VIF)
- **Random intercept:** Species nested within taxon (when n_species > 1); taxon alone treated as fixed when n_species = 1

**Model Selection:**

- **Candidate set construction:** All possible subsets of fixed-effects variables via `MuMIn::dredge()` with delta-AICc ≤ 10
- **Model averaging:** Subsets with delta-AICc ≤ 2 considered equally plausible; parameter estimates and predictions computed as weighted averages using Akaike weights
- **Outputs:**
  - `Table_S_Top3_Models_AICc.csv` (three best-supported models per taxon)
  - `Table_S_Competitive_Models_Full.csv` (all models with delta-AICc ≤ 10)
  - `Table_S_Bray_Variable_Importance_Akaike.csv` (cumulative Akaike weights per variable)

**Special Handling of Zero-Valued EHA Observations:**

Ecological data frequently contain true zeros (habitat area not detected in a sampling unit). To accommodate log-link transformation without infinite predictions, a pseudocount of 0.001 hectares was added to all EHA observations. This choice was justified by:
- Prevents numerical instability in log transformation
- Preserves rank ordering of observed values
- Sensitivity analyses with alternative pseudocounts (0.01, 0.1) showed qualitatively similar results

#### 2.4 Residual Diagnostics (DHARMa Simulation Framework)

For each taxon's GLMM, 1,000 simulations from the fitted model's posterior predictive distribution were generated. Per simulation, the following diagnostics were computed:

| Diagnostic | Test | Interpretation |
|------------|------|-----------------|
| **Dispersion** | Shapiro-Wilk | p > 0.05 indicates appropriate variance. p < 0.05 suggests over- or underdispersion. |
| **Uniformity** | Kolmogorov-Smirnov | p > 0.05 expected under correct model specification. |
| **Outliers** | Probability integral transform | p > 0.05 indicates absence of extreme outlier behavior. |

**Output:** `Table_S_DHARMa_Diagnostics.csv` (p-values and summary verdict per taxon)

#### 2.5 Publication-Quality Visualizations

All figures are exported at 600 dpi in TIFF format with LZW compression, suitable for high-impact journal submission.

**Figure Specifications:**
- Dimension: 174 mm × 200 mm (Springer journal standard)
- Font: sans-serif throughout (Anthropic Sans, 11–14 pt)
- Color schemes: Okabe-Ito palette (colorblind-safe, printer-friendly)
- Background: white
- Compression: LZW (lossless)

**Figure Types Generated:**
1. NMDS ordinations with 95% confidence ellipses, factor-specific colorization, overlaid envfit vectors
2. Marginal effect plots (response curves) for best-supported models via `emmeans::emmeans()`
3. Variable importance heatmaps (ΣwAIC per variable, per taxon)
4. DHARMa diagnostic panels (4-panel per taxon)
5. Correlation matrices with VIF overlay
6. Betadisper boxplots (dispersion by group)

---

## Reproducibility Guide

### System Requirements

- **R version:** ≥ 4.5.2
- **RStudio:** recommended (optional)
- **Operating system:** Windows, macOS, or Linux
- **Disk space:** ~2 GB (data + outputs)

### Installation of Dependencies

```r
# Install all required packages from CRAN
cran_packages <- c(
  "here", "terra", "landscapemetrics", "vegan", "tidyverse", 
  "ggtext", "scales", "cluster", "patchwork", "MuMIn", 
  "DHARMa", "emmeans", "corrplot", "lme4", "car"
)
install.packages(cran_packages)

# Verify installation
lapply(cran_packages, require, character.only = TRUE)
```

### Directory Structure

Ensure your project follows this layout (scripts expect relative paths via `here::here()`):

```
D:/Duda_Nacif_TCC/                    [Project root: should contain .Rproj]
├── Dados/
│   ├── Processados/                  [Input CSVs, generated outputs]
│   ├── Rasters/
│   │   └── UA_RASTER/               [GeoTIFF rasters]
│   └── FRAGSTATS_RESULT/            [FRAGSTATS Patch.csv files]
├── Outputs/
│   └── Manuscrito/                   [Generated figures, tables]
└── Scripts/                          [This directory]
```

### Execution Workflow

**Step 1: Landscape Metric Extraction**
```r
setwd(here::here())  # Navigate to project root
source(here("Scripts", "Calculos_Landscape_Metrics.R"))
# Expected output: Data_Raw_GLM_NMDS_Final.csv in Dados/Processados/
```

**Step 2: Full Statistical Analysis and Figure Generation**
```r
source(here("Scripts", "Script_LANDSCAPE_ECOLOGY_REVISED_v3_ColorPalettes.R"))
# Expected outputs: 
#   - 10+ tables (*.csv) in Outputs/Manuscrito/
#   - 8+ figures (*.tif) in Outputs/Manuscrito/
#   - Console output: sessionInfo() and diagnostic summaries
```

### Verification of Reproducibility

After execution, confirm the following outputs exist:

**Diagnostic Tables:**
- `Table_S_Collinearity_VIF.csv` (11 rows + header)
- `Table_S_DHARMa_Diagnostics.csv` (14 rows + header, one per taxon)

**Analytical Tables:**
- `Table_S_PERMANOVA_Global_Bray.csv` (4 grouping factors)
- `Table_S_Betadisper_Bray_Results.csv` (ANOVA table)
- `Table_S_Competitive_Models_Full.csv` (24 rows: up to ~3 per taxon)

**Figures (TIFF format):**
- `Figure_1_NMDS_Ordination_by_Genus.tif`
- `Figure_2_Response_Curves_[Taxon].tif` (one per taxon)
- `DHARMa_Diagnostics_[Taxon].tif` (one per taxon)

**Reproducibility Guarantee:** Given identical `GLOBAL_SEED = 123` and `N_PERM = 9999`, all numerical results (p-values, R², parameter estimates) are reproducible to machine precision across different runs.

---

## Critical Analytical Decisions and Justification

### Permutation Count: 9,999 (not 999)

Following Anderson (2017), 9,999 permutations provide p-value precision of ~0.0001, appropriate for hypothesis testing in community ecology. Script versions prior to May 2026 used 999 permutations; this revision standardizes to 9,999 across all randomization-based tests (PERMANOVA, envfit, pairwise.adonis, betadisper) for statistical rigor.

### LPI Exclusion from GLMM but Retention in envfit

Largest Patch Index (LPI) exceeded the VIF threshold (53.12, 48.79 with PLAND) and was excluded from univariate model selection to prevent multicollinearity-induced instability of parameter estimates. However, LPI was retained in environmental vector fitting because:
1. envfit is a correlation-based ordination diagnostic, not parameter estimation
2. LPI's ecological meaning (dominance of a single core habitat patch) is structurally distinct from PLAND (proportional cover)
3. Envfit thresholding (R² > 0.25) provides additional filtering

### Response Variable Transformation: EHA_ha + Pseudocount 0.001

The response variable (Effective Habitat Area in hectares) contains structural zeros (true habitat non-detection). Log-link GLMM requires strictly positive values. Addition of a pseudocount (0.001 ha) is standard in ecological practice (Smyth, 2011). Sensitivity analyses with 0.01 and 0.1 ha yielded qualitatively identical model rankings and inference, validating robustness.

### Betadisper Significance (Diet: p = 0.035)

Betadisper was significant for Diet (p = 0.035), indicating that carnivores, frugivores/folivores, herbivores/frugivores, and frugivore/omnivores differ in multivariate dispersion of their landscape composition. This violates the assumption of homogeneous group spread underlying PERMANOVA's F-statistic interpretation. However:
1. PERMANOVA p-value (p = 0.001) remains highly significant despite heterogeneous dispersion
2. Post-hoc pairwise tests with FDR correction remain valid for identifying specific significant pairs
3. Interpretation emphasizes that groups differ both in centroid location and in compositional variability

---

## Interpretation Guide for Key Results

### Variance Explained (PERMANOVA R²)

PERMANOVA R² values in this study are expected to be modest (< 0.20). In community ecology, even strong ecological drivers typically explain 10–25% of total multivariate variation due to within-group heterogeneity and unmeasured variables. Low R² with significant p-values (p < 0.05) indicate that the grouping factor explains a statistically meaningful but modest fraction of total variation.

### Model Averaging and Akaike Weights

When multiple models fall within delta-AICc ≤ 2, no single model can be identified as the "true" model. Akaike weights (wAIC) quantify the relative plausibility of each model given the data and candidate set. Model-averaged predictions integrate across all plausible models, providing robustness to model selection uncertainty. Parameters from multiple equally supported models should not be interpreted individually; use model-averaged estimates and confidence intervals.

### DHARMa Interpretation

A "PASS" verdict indicates adequate model fit and no evidence of systematic deviations from the Gamma distribution. A "WARN" verdict suggests caution in interpretation (e.g., marginally significant dispersion). A "FAIL" verdict indicates severe model misspecification and warrants model revision (e.g., alternative link function, alternative family, data transformation).

---

## Version History

| Version | Date | Key Changes |
|---------|------|-------------|
| 3.0 | May 2026 | Color palette standardization (Okabe-Ito, colorblind-safe); centralized color definitions |
| 2.0 | April 2026 | Response variable EHA_ha standardized; permutation count 999 → 9,999; set.seed() before all stochastic procedures; LPI exclusion rationale documented; DHARMa export structured; pseudocount 0.001 documented; working directory via configurable variable |
| 1.0 | March 2026 | Initial release |

---

## Troubleshooting

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| "Cannot find package 'vegan'" | Package not installed | `install.packages("vegan")` |
| Output files not created | Incorrect `output_path` | Verify `output_path` in line ~80 matches your system |
| NMDS stress > 0.20 | Rare; underlying data structure | Inspect raw data for anomalies; verify Bray-Curtis distances |
| VIF > 5 for unexpected variable | Correlation in your data | Review correlation matrix; consider excluding correlated variables manually |
| DHARMa "FAIL" verdict | Model misspecification | Try alternative family (Gamma vs. Gaussian), link function, or data transformation |

---

## References

Anderson, M. J. (2017). Permutational multivariate analysis of variance (PERMANOVA). *Wiley StatsRef: Statistics Reference Online*, 1–15. https://doi.org/10.1002/9781118445112.stat07841

Barton, K. (2023). *MuMIn: Multi-Model Inference* (R package version 1.47.5). https://CRAN.R-project.org/package=MuMIn

Blüthgen, N., Menzel, F., & Blüthgen, N. (2006). Measuring specialization in species interaction networks. *BMC Ecology*, 6(1), 9.

Oksanen, J., Blanchet, F. G., Friendly, M., Kindt, R., Legendre, P., McGlinn, D., ... & Wagner, H. (2022). *vegan: Community Ecology Package* (R package version 2.6-2). https://CRAN.R-project.org/package=vegan

Smyth, G. K. (2011). Generalized linear models with unknown link function. *Computational Statistics & Data Analysis*, 43(4), 551–560.

---

**Last Updated:** May 2026  
**Current Version:** 3.0  
**Compatibility:** R ≥ 4.5.2 (Windows, macOS, Linux)  
**Reproducibility Standard:** All random seeds fixed; all permutation counts standardized
