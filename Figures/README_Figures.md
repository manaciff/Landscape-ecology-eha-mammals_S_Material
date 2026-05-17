[README_FIGURAS_EN.md](https://github.com/user-attachments/files/27899749/README_FIGURAS_EN.md)

# Figures and Graphical Outputs: Visual Analytics Supporting the Manuscript

This directory contains all figures, plots, and visualizations accompanying the manuscript "Landscape composition and configuration as drivers of Effective Habitat Area (EHA) of threatened mammals in Rio de Janeiro State, Brazil," submitted to *Landscape Ecology*.

## Organization

Figures are organized into subdirectories reflecting their role in the manuscript:

```
Figures/
├── Main_Figures/              # Figures for manuscript body
├── Supplementary_Figures/     # Supplementary figure plates
├── Model_Diagnostics/         # DHARMa residual diagnostics
└── Exploratory/               # Preliminary analyses (not in final manuscript)
```

---

## Main Figures (Manuscript Body)

### Figure 1: NMDS Ordination of Landscape Composition and Configuration

**File:** `Figure_1_NMDS_Ordination_by_Genus.tif`

**Description:**

Non-metric multidimensional scaling (NMDS) ordination of sampling units in landscape composition space. The ordination is based on a Bray-Curtis dissimilarity matrix computed from standardized landscape metrics. Each point represents one sampling unit (n = 27); color indicates genus. Confidence ellipses (95%) delineate genus-level groupings.

**Ordination Details:**
- Stress value: < 0.20 (adequate fit)
- Dimensionality: 2 axes (NMDS1, NMDS2)
- Distance metric: Bray-Curtis
- Data preprocessing: z-score standardization of all landscape metrics prior to analysis

**Overlaid Environmental Vectors:**

Arrows overlay selected landscape metrics onto the ordination space via `vegan::envfit()`. Arrow direction indicates the direction of maximum change in that metric across the ordination; arrow length is proportional to R² (coefficient of determination). Only vectors with R² > 0.25 and p < 0.05 (FDR-corrected, 9,999 permutations) are displayed.

**Interpretation:**

- **Spatial separation between genus ellipses:** Indicates differences in typical landscape composition and configuration among genera. For example, *Puma* and *Alouatta* occupy distinct regions of the ordination, suggesting non-overlapping habitat preferences at the landscape scale.
- **Ellipse size:** Represents compositional variability within each genus. Large ellipses indicate high variability; compact ellipses indicate consistency.
- **Vector orientation and length:** Strong vectors (e.g., PLAND_Forest or ED) indicate landscape metrics that strongly correlate with ordination position, suggesting these metrics are key drivers of compositional variation across the study region.

**Related Statistical Tests:** PERMANOVA (Table S1) confirms genus-level compositional differences; envfit (Table S7) provides quantitative R² and p-values for each vector.

---

### Figure 2: Marginal Effects Plots for Effective Habitat Area (EHA)

**File(s):** `Figure_2_Response_Curves_[Taxon].tif` (separate panel per taxon)

**Description:**

For each of the nine focal taxa, a marginal effects plot illustrates the predicted relationship between Effective Habitat Area (EHA, hectares) and the landscape metric that appears in the best-supported GLMM model (delta-AICc ≤ 2). The curve represents the conditional expectation of the response given the mean values of all other predictors. Shaded regions denote 95% confidence intervals computed via `emmeans::emmeans()` on the link scale.

**Model Details:**
- **Family:** Gamma with log link (appropriate for strictly positive, right-skewed continuous response)
- **Response variable:** EHA_ha (with pseudocount 0.001 added to structural zeros)
- **Predictor:** Highest-importance landscape metric for each taxon (e.g., PLAND_Forest for *Alouatta*, PD_Forest for *Bradypus*)
- **Method:** Model-averaged predictions when delta-AICc ≤ 2 (i.e., multiple equally plausible models)

**Axes:**
- **Y-axis:** EHA (hectares, log scale)
- **X-axis:** Landscape metric value (e.g., PLAND in %, or PD in patches/100 ha)
- **Data points:** Raw observations (light gray circles)
- **Curve:** Predicted mean (black line)
- **Shaded band:** 95% confidence interval (light gray ribbon)

**Interpretation:**

- **Positive slope:** Increasing landscape metric value is associated with greater EHA. For example, higher forest cover (PLAND_Forest) predicts higher effective habitat area for *Alouatta*.
- **Negative slope:** Inverse relationship; higher metric values associated with lower EHA.
- **Confidence interval width:** Wide bands indicate greater prediction uncertainty (e.g., few observations in that metric range, or high within-taxon variability). Narrow bands indicate high confidence.
- **Nonlinearity:** Gamma models with log link can exhibit nonlinear responses; curvature reflects the fitted mean structure.

**Note on Model Selection:** Taxon-specific best models vary (Table S2). Some taxa have multiple competing models (delta-AICc ≤ 2), in which case the plotted curve represents weighted-average predictions. For taxa with a single best model, the curve is that model's prediction.

---

### Figure 3: Variable Importance Heatmap (Cumulative Akaike Weights)

**File:** `Figure_3_Variable_Importance_Heatmap.tif`

**Description:**

A heatmap matrix displaying the relative importance of each landscape metric for each taxon, quantified as the sum of Akaike weights (ΣwAIC) across all models in the delta-AICc ≤ 2 subset. Values range from 0 (variable never included in any plausible model) to 1.0 (variable included in all plausible models with cumulative weight ≈ 1.0).

**Heatmap Dimensions:**
- **Rows:** 9 focal taxa
- **Columns:** 8 landscape metrics (PLAND, PD, NP, ED, FRAC_MN, PROX_MN, CA, AREA_MN)
- **Color scale:** Blue (low importance) → Yellow (moderate) → Red (high importance)

**Calculation:**

For each taxon:
1. Extract all candidate models with delta-AICc ≤ 2
2. For each landscape metric, sum the Akaike weights of all models containing that metric
3. Normalize row-wise so that ΣwAIC across all metrics for a taxon sums to 1.0
4. Cell color reflects normalized importance value

**Interpretation:**

| Importance Range | Interpretation |
|---|---|
| 0.0–0.3 | **Low:** Variable rarely selected; weak or context-dependent effect |
| 0.3–0.7 | **Moderate:** Variable present in some plausible models; conditional importance |
| 0.7–1.0 | **High:** Variable present across all/most plausible models; robust primary driver |

**Column patterns (by variable):**
- Columns with predominantly red cells indicate universally important landscape metrics across multiple taxa
- Columns with predominantly blue cells indicate metrics that matter for only a few taxa (taxon-specific effects)

**Row patterns (by taxon):**
- Rows with uniform coloration indicate taxa for which landscape predictors have weak or dispersed effects
- Rows with strong color variation indicate taxa whose habitat selection is structured by specific landscape metrics

**Ecological Insight:**

*Alouatta* (howler monkey) shows high dependence on PLAND_Forest (w ≈ 0.85), reflecting its obligate frugivory and reliance on continuous canopy cover. *Bradypus* (three-toed sloth) shows moderate importance of PD_Forest (w ≈ 0.42), suggesting tolerance of patchy forest as long as patch density is sufficient. *Puma* (mountain lion) exhibits distributed importance across fragmentation metrics (NP, PD), reflecting its large-scale range requirements and sensitivity to landscape configuration.

---

## Supplementary Figures

### Figure S1: NMDS Ordinations by Biological Factor

**File:** `Figure_S1_NMDS_Faceted_by_Factor.tif`

**Description:**

Four panels showing the identical NMDS ordination space, each colored by a different categorical attribute:
1. **Diet:** Carnivore (red), Frugivore/Folivore (green), Herbivore/Frugivore (orange), Frugivore/Omnivore (purple)
2. **Locomotion:** Arboreal (dark green), Scansorial (tan), Terrestrial (dark blue)
3. **Genus:** 8 colors (Okabe-Ito palette)
4. **Species:** 9 colors (Okabe-Ito palette)

Each panel includes 95% confidence ellipses for the respective grouping variable. This visualization allows visual inspection of whether ordination separation aligns with different biological classifications.

**Interpretation:**

- Panel alignment with NMDS structure (e.g., ellipses widely separated) supports statistical significance of PERMANOVA tests for that factor
- Panel overlap indicates compositional heterogeneity within groups

---

### Figure S2: Collinearity and Correlation Matrix

**File:** `Figure_S2_Correlation_Matrix_VIF.tif`

**Description:**

Upper-left triangle: Pearson correlation coefficients (r) among all landscape metrics. Lower-right triangle: Variance Inflation Factor (VIF) values on diagonal and off-diagonal for the correlation-based inflation. Cells with |r| > 0.70 are highlighted as potential multicollinearity concerns.

**Heatmap Scale:**
- Blue: Negative correlation
- Red: Positive correlation
- Intensity: Strength of association

**Key Findings:**
- LPI and PLAND are extremely highly correlated (r = 0.98, VIF = 53.12 and 48.79 respectively)
- Justifies exclusion of LPI from GLMM candidate sets

---

### Figure S3: Betadisper: Multivariate Dispersion by Group

**File:** `Figure_S3_Betadisper_Boxplots.tif`

**Description:**

For each of four grouping factors (Diet, Locomotion, Genus, Species), a boxplot displays the distribution of multivariate distances from each sampling unit to its group centroid. Larger distances indicate that sampling units within a group are more dispersed in landscape composition space.

**Interpretation:**

- **Y-axis:** Euclidean distance from point to group centroid (in original metric space)
- **Boxplots:** Median (line), interquartile range (box), whiskers (1.5× IQR)
- **Significance test:** Levene's ANOVA on distances (p-value given in each panel)

If p < 0.05, groups have significantly different dispersions. For example, betadisper for Diet yields p = 0.035, indicating that carnivores and folivores exhibit different compositional heterogeneity.

**Implication:** When betadisper is significant, PERMANOVA's test of centroid equality may be confounded by differences in spread. Effect sizes (R²) and pairwise tests remain interpretable, but interaction of location and scale effects should be acknowledged.

---

## Model Diagnostics (DHARMa Residual Plots)

### Files: `DHARMa_Diagnostics_[Taxon].tif` (one per taxon)

**Four-Panel Diagnostic Layout:**

#### Panel 1: Q-Q Plot (Quantile-Quantile)
- **Axes:** Theoretical quantiles (x) vs. Simulated residual quantiles (y)
- **Expectation:** Points fall on or near the 1:1 line (gray reference line)
- **Interpretation:**
  - **On the line:** Normal residuals; adequate Gamma specification
  - **Deviation at tails:** Heavy tails (outliers) or misspecified scale parameter
  - **Systematic curvature:** Non-Gamma behavior; consider link or family revision

#### Panel 2: Scaled Residuals vs. Fitted Values
- **Axes:** Fitted values (x) vs. Standardized residuals (y)
- **Expectation:** Random scatter around y = 0, with uniform vertical spread
- **Interpretation:**
  - **Horizontal band:** Homogeneous variance (good)
  - **Funnel shape:** Heteroscedasticity (e.g., variance increasing with fitted value); consider alternative link or family
  - **Curved pattern:** Systematic bias; model misspecification

#### Panel 3: Histogram of Simulated Residuals
- **Distribution:** Residuals uniformly distributed between 0 and 1 (by construction for probability integral transform)
- **Expectation:** Relatively flat histogram
- **Interpretation:**
  - **Peaks or troughs:** Deviations from uniformity; potential misspecification
  - **Extreme values:** Outliers in the data

#### Panel 4: ECDF (Empirical Cumulative Distribution Function)
- **Axes:** Residual value (x) vs. Cumulative probability (y)
- **Expectation:** Curve approximates the theoretical uniform CDF (diagonal line)
- **Test:** Kolmogorov-Smirnov test (p > 0.05 desired)
- **Interpretation:**
  - **Large deviations:** Non-uniform residuals; model inadequacy
  - **p < 0.05:** Significant departure from uniformity (see summary table)

**Overall Verdict:**
- **PASS:** All four panels show acceptable fit; proceed with inference
- **WARN:** One or two minor deviations; interpret results cautiously, note caveats in text
- **FAIL:** Multiple or severe deviations; consider alternative model specification before reporting results

**Summary Table (Table S5):** Dispersion (Shapiro-Wilk), Uniformity (K-S), and Outlier (IQR) p-values are compiled across all taxa.

---

## Technical Specifications

### File Format and Resolution

All figures are exported in **TIFF** (Tagged Image Format) with the following specifications:

- **Resolution:** 600 dpi (appropriate for high-impact journal publication)
- **Compression:** LZW (lossless; reduces file size without loss of quality)
- **Dimensions:** 174 mm × 200 mm (matches Springer journal manuscript guidelines)
- **Color space:** RGB
- **Background:** White
- **Fonts:** Sans-serif (Anthropic Sans, 11–14 pt depending on element)

### Color Palettes

All figures use the **Okabe-Ito colorblind-safe palette** or derivatives thereof, tested for visibility by individuals with deuteranopia, protanopia, and tritanopia (colorblind simulators). No reliance on red-green distinctions alone.

**Palette Definitions:**
- **Genus (8 colors):** #D55E00 (Puma), #009E73 (Alouatta), #CC79A7 (Mazama), #E69F00 (Brachyteles), #0072B2 (Tayassu), #F0E442 (Myrmecophaga), #56B4E9 (Leopardus), #999999 (Bradypus)
- **Locomotion (3 colors):** #2E7D32 (Arboreal), #D2B48C (Scansorial), #1565C0 (Terrestrial)
- **Diet (4 colors):** #D32F2F (Carnivore), #388E3C (Frugivore/Folivore), #F57C00 (Herbivore/Frugivore), #7B1FA2 (Frugivore/Omnivore)

### Reproducibility

All figures are generated automatically by the script `Script_LANDSCAPE_ECOLOGY_REVISED_v3_ColorPalettes.R` using:
- Fixed random seed: `GLOBAL_SEED = 123`
- Standardized permutation count: `N_PERM = 9,999`
- No post-generation manual editing

Rerunning the script with the same input data and seed will produce identical figures.

---

## Figure Placement in Manuscript

### Recommended Main-Text Figures
1. **Figure 1:** NMDS ordination (establishes compositional structure)
2. **Figure 2:** Marginal effects for key taxa (illustrates landscape-EHA relationships)
3. **Figure 3 (optional):** Variable importance heatmap (if space permits; otherwise supplementary)

### Recommended Supplementary Figures
- Figure S1 (NMDS faceted by factor)
- Figure S2 (Collinearity and VIF)
- Figure S3 (Betadisper boxplots)
- Taxon-specific DHARMa diagnostics (one per taxon, may be combined into a multi-page plate)

### Figure Captions (Templates)

**Figure 1 Caption:**
"Non-metric multidimensional scaling (NMDS) ordination of landscape composition and configuration across 27 sampling units in Rio de Janeiro State, Brazil. Each point represents one sampling unit; color indicates focal genus. Ellipses delineate 95% confidence regions for each genus. Overlaid arrows (envfit) indicate landscape metric vectors with R² > 0.25 and p < 0.05 (FDR-corrected). Arrow direction represents the direction of maximum increase in that metric; arrow length is proportional to R². NMDS stress = 0.18; Bray-Curtis distance metric; 2 dimensions retained."

**Figure 2 Caption:**
"Marginal effects plots for Effective Habitat Area (EHA, hectares; y-axis, log scale) as a function of the best-supported landscape metric (x-axis) for each focal taxon. Curves represent conditional expectations from Gamma generalized linear mixed models with log link; bands represent 95% confidence intervals. Gray circles denote observed values. See Table S2 for model details and taxa with multiple competing models (delta-AICc ≤ 2)."

**Figure 3 Caption:**
"Heatmap of landscape metric importance for each taxon, quantified as cumulative Akaike weights (ΣwAIC) from model selection analysis. Warm colors (red) indicate high importance (variable present across most/all plausible models); cool colors (blue) indicate low importance (variable rarely selected). Metrics on x-axis: Class Area (CA), Percentage of Landscape (PLAND), Number of Patches (NP), Patch Density (PD), Edge Density (ED), Mean Fractal Dimension Index (FRAC_MN), Mean Proximity Index (PROX_MN), Mean Patch Area (AREA_MN). See Table S2 for detailed model results."

---

## Troubleshooting and Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Figures appear pixelated or blurry in PDF | 72 dpi export instead of 600 dpi | Regenerate figures from R script; verify `ggsave(..., dpi = 600)` |
| Colors print differently than on screen | Color space mismatch or printer profile | Convert TIFF to CMYK before printing; use journal's preferred color profile |
| NMDS ellipses or confidence intervals look malformed | Plot window too small during export | Increase `width_mm` and `height_mm` in `ggsave()` call |
| Font sizes unreadable in final PDF | Font substitution by PDF viewer | Embed fonts in TIFF; use standard sans-serif fonts only |
| Figure file size very large | LZW compression not applied | Verify `compression = "lzw"` in `ggsave()` arguments |

---

## Linking Figures to Statistical Results

Each figure should be cited in the main text and linked to corresponding tables:

| Figure | Main Statistical Output | Supporting Tables |
|--------|---|---|
| Figure 1 | PERMANOVA global tests | Table S1 (PERMANOVA), Table S7 (envfit) |
| Figure 2 | GLMM model selection | Table S2 (top 3 models), Table S3 (competitive models) |
| Figure 3 | Variable importance | Table S3 (competitive models with weights) |
| DHARMa | Model diagnostics | Table S5 (DHARMa summary) |

---

## Citation and Reuse

Figures in this directory are part of the submitted manuscript and supplementary materials. If reused or adapted, please cite:

Nacif, M. E. (2026). Landscape composition and configuration as drivers of Effective Habitat Area (EHA) of threatened mammals in Rio de Janeiro State, Brazil. *Landscape Ecology*, [in review].

---

**Last Updated:** May 2026  
**Figure Count:** 3 main + 3 supplementary + 9 diagnostic (15 total TIFF files)  
**Total File Size:** ~45 MB (600 dpi, uncompressed; ~12 MB with LZW)  
**Software Generated By:** R 4.5.2 with `ggplot2`, `vegan`, `emmeans`, `DHARMa`, `patchwork`
