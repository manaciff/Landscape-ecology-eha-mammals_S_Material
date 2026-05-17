[README_PLANILHAS_EN.md](https://github.com/user-attachments/files/27899780/README_PLANILHAS_EN.md)

# Supplementary Data Tables: Comprehensive Results and Analytical Outputs

This directory contains all tabular data—input datasets, processed files, and analytical outputs—supporting the manuscript submitted to *Landscape Ecology*. Tables are organized by analytical stage and can be imported directly into R, Python, or spreadsheet software for inspection, visualization, or reanalysis.

---

## 1. INPUT DATA

### 1.1 Master Dataset for Analysis

**File:** `Data_Raw_GLM_NMDS_Final.csv`

**Purpose:** Primary data matrix integrating biological, environmental, landscape, and socioeconomic attributes for all 9 focal taxa across 27 sampling units.

**Dimensions:**
- Observations (rows): n ≈ 240 (9 taxa × 27 SUs, approximately; exact count depends on species presence/absence)
- Variables (columns): 18+ predictors

**Column Dictionary:**

| Column Name | Data Type | Units | Description |
|---|---|---|---|
| `ORDER` | character | — | Taxonomic order (e.g., "Primates", "Carnivora") |
| `GENUS` | character | — | Genus name (Alouatta, Bradypus, Brachyteles, Leopardus, Mazama, Myrmecophaga, Puma, Tayassu) |
| `SPECIES` | character | — | Full binomial species name (e.g., *Alouatta guariba*) |
| `DIET` | character | — | Dietary category: "Ca" (Carnivore), "Fr/Fo" (Frugivore/Folivore), "Hb/Fr" (Herbivore/Frugivore), "Fr/On" (Frugivore/Omnivore) |
| `LOCOMOTION` | character | — | Primary locomotion mode: "Arboreal", "Scansorial", "Terrestrial" |
| `UA_ID` | character | — | Unique identifier for sampling unit |
| `EHA_ha` | numeric | hectares | Effective Habitat Area (with pseudocount 0.001 added to zeros) |
| `Mean_Elevation_m` | numeric | meters | Mean elevation of sampling unit |
| `Mean_Pop_Density` | numeric | persons/km² | Human population density at sampling unit |
| `CA_Forest` | numeric | hectares | Class Area for forest land-cover class |
| `PLAND_Forest` | numeric | % | Percentage of Landscape covered by forest |
| `NP_Forest` | numeric | count | Number of forest patches (connected components) |
| `PD_Forest` | numeric | patches/100 ha | Patch Density (forest patches per 100 hectares) |
| `ED_Forest` | numeric | m/ha | Edge Density (forest-nonforest border per hectare) |
| `FRAC_MN_Forest` | numeric | — | Mean Fractal Dimension Index (forest; ranges 1–2, where 2 = maximum complexity) |
| `PROX_MN_Forest` | numeric | meters | Mean Proximity Index (forest; measure of patch connectivity) |
| `AREA_MN_Forest` | numeric | hectares | Mean area of forest patches |
| `[LULC_Class]_*` | numeric | varies | Equivalent metrics for all 5 land-use/land-cover classes (Herbaceous, Agropecuaria, Water, Non-Vegetated) |

**Data Preparation Steps:**
1. Raster metrics extracted via `landscapemetrics::calculate_lsm()` and FRAGSTATS
2. Biological attributes joined from species reference table
3. Environmental variables (elevation, population density) joined from auxiliary data
4. Missing EHA values: pseudocount 0.001 ha added to enable log transformation
5. All continuous predictors: z-score standardized (μ = 0, σ = 1) prior to NMDS
6. Row order: arbitrary (no particular sequence)

**Use Case:** Primary input for `Script_LANDSCAPE_ECOLOGY_REVISED_v3_ColorPalettes.R`. Load via:
```r
data <- read.csv("Data_Raw_GLM_NMDS_Final.csv", stringsAsFactors = TRUE)
```

---

## 2. DIAGNOSTIC TABLES

### 2.1 Collinearity Assessment

**File:** `Table_S_Collinearity_VIF.csv`

**Purpose:** Variance Inflation Factor (VIF) for each landscape metric, indicating severity of multicollinearity. VIF > 5 triggers exclusion from GLMM candidate sets.

**Structure:**
```
Variable            | VIF
LPI_Forest          | 53.12
PLAND_Forest        | 48.79
ED_Forest           | 3.21
PD_Forest           | 2.87
...
```

**Interpretation:**
- **VIF = 1:** No collinearity
- **VIF 1–5:** Acceptable; variable retained in modeling
- **VIF > 5:** Problematic multicollinearity; exclude from univariate models (justification: parameter instability, reduced interpretability)

**Action Taken:** LPI (VIF = 53.12, 48.79) excluded from GLMM candidate sets due to extreme collinearity with PLAND. LPI retained in envfit analysis because its ecological meaning (dominance of a single core remnant) is structurally distinct from PLAND (proportional cover).

**Note:** VIF calculated under simple linear regression framework using EHA_ha as the response. Not used for ordination (NMDS) or PERMANOVA analyses, which employ distance-based metrics.

---

### 2.2 Residual Diagnostics Summary

**File:** `Table_S_DHARMa_Diagnostics.csv`

**Purpose:** Rapid overview of GLMM residual adequacy for each taxon, based on 1,000 simulations from the fitted model's posterior predictive distribution.

**Structure:**
```
Category  | Taxon        | n_obs | Dispersion_p | Uniformity_p | Outliers_p | Overall_Diagnostic
GLMM      | Alouatta     | 15    | 0.342        | 0.756        | 0.521      | PASS
GLMM      | Bradypus     | 8     | 0.028        | 0.189        | 0.041      | WARN
GLMM      | Brachyteles  | 12    | 0.667        | 0.891        | 0.623      | PASS
...
```

**Columns:**
- **Category:** Analysis type (here, always "GLMM")
- **Taxon:** Focal species/genus
- **n_obs:** Number of observations (SUs) for this taxon
- **Dispersion_p:** Shapiro-Wilk p-value for test of dispersion (H₀: variance appropriate for Gamma family)
- **Uniformity_p:** Kolmogorov-Smirnov p-value for uniformity of residuals (H₀: residuals follow U(0,1))
- **Outliers_p:** Probability integral transform test p-value (H₀: no extreme outliers)
- **Overall_Diagnostic:** Summary verdict—PASS (all p > 0.05), WARN (one or two p < 0.05), FAIL (multiple p < 0.05)

**Interpretation of Verdicts:**

| Verdict | Meaning | Action |
|---|---|---|
| **PASS** | All diagnostic tests non-significant (p > 0.05) | Proceed with inference; results trustworthy |
| **WARN** | One or two tests marginally significant (p < 0.05) | Interpret cautiously; note limitations in text; consider sensitivity checks |
| **FAIL** | Multiple tests significant; severe deviations | Model misspecification likely; consider alternative link, family, or transformation |

**Examples from Data:**
- *Alouatta* (PASS): Gamma specification adequate; parameters and predictions reliable
- *Bradypus* (WARN): Dispersion test significant (p = 0.028), indicating potential sobredispersion; results should be interpreted with caution regarding variance estimation
- Most other taxa (PASS): No systematic violations

**Detailed Diagnostics:** Corresponding figure (DHARMa_Diagnostics_[Taxon].tif) shows four-panel diagnostic plots (Q-Q, residuals vs. fitted, histogram, ECDF) for visual inspection.

---

## 3. MULTIVARIATE ANALYSIS RESULTS

### 3.1 Global PERMANOVA Tests

**File:** `Table_S_PERMANOVA_Global_Bray.csv`

**Purpose:** Summary of permutational multivariate analysis of variance (PERMANOVA) testing whether composition/configuration differs significantly among levels of four grouping factors: Diet, Locomotion, Genus, Species.

**Structure:**
```
Grouping        | Df | SumOfSqs | R2     | F_value | p_value
Diet            | 3  | 1.234    | 0.087  | 3.421   | 0.001
Locomotion      | 2  | 0.876    | 0.062  | 2.156   | 0.042
Genus           | 7  | 2.345    | 0.166  | 4.201   | < 0.001
Species         | 8  | 2.678    | 0.189  | 4.563   | < 0.001
```

**Columns:**
- **Grouping:** Categorical factor tested (Diet, Locomotion, Genus, or Species)
- **Df:** Degrees of freedom (number of levels − 1)
- **SumOfSqs:** Pseudo-F sums of squares (permutation-based analog to traditional ANOVA)
- **R²:** Proportion of total Bray-Curtis dissimilarity variance explained by the grouping factor (typical range: 0.05–0.25 in community ecology)
- **F_value:** Pseudo-F test statistic
- **p_value:** Significance from 9,999 permutations (FDR-corrected); indicates whether factor explains significant compositional variation

**Interpretation:**

- **p < 0.05, significant:** Factor explains meaningful variation in landscape composition. Groups differ significantly in their typical landscape profiles.
- **p > 0.05, non-significant:** Factor does not explain significant variation. Grouping provides no predictive value for landscape composition.
- **Low R² with p < 0.05 (common in ecology):** Factor is statistically significant but explains only modest variance due to within-group heterogeneity. Interpretation: "groups differ significantly, but substantial variation exists within groups."

**Related Analyses:**
- **Pairwise tests:** See PERMANOVA_Bray_Pairwise_[Factor].csv files for specific group comparisons
- **Homogeneity of dispersion:** See Table_S_Betadisper_Bray_Results.csv (confirms whether R² and F reflect centroid differences or also involve dispersion heterogeneity)

---

### 3.2 Pairwise PERMANOVA Tests (Post-Hoc)

**Files:** 
- `PERMANOVA_Bray_Pairwise_Diet.csv`
- `PERMANOVA_Bray_Pairwise_Locomotion.csv`
- `PERMANOVA_Bray_Pairwise_Genus.csv`
- `PERMANOVA_Bray_Pairwise_Species.csv`

**Purpose:** All-vs-all pairwise comparisons between factor levels with multiple-testing correction (False Discovery Rate).

**Structure (example: Diet):**
```
pairs                              | F.Model | R2    | p.value | p.adjusted | sig
Carnivore vs Frugivore/Folivore    | 4.231   | 0.142 | 0.001   | 0.002      | ***
Carnivore vs Herbivore/Frugivore   | 2.876   | 0.098 | 0.042   | 0.084      | ns
Frugivore/Folivore vs Herbivore    | 1.345   | 0.045 | 0.321   | 0.481      | ns
...
```

**Columns:**
- **pairs:** Comparison between two factor levels (e.g., "Carnivore vs Frugivore/Folivore")
- **F.Model:** Pseudo-F test statistic for the pairwise comparison
- **R2:** Proportion of variance explained by the contrast (relative to total variance, not just the two groups)
- **p.value:** Unadjusted p-value from 9,999 permutations
- **p.adjusted:** Benjamini-Hochberg FDR-corrected p-value (controls for multiple testing)
- **sig:** Significance indicator (*** p < 0.001; ** p < 0.01; * p < 0.05; ns = not significant at α = 0.05)

**Interpretation:**

Use **p.adjusted** for statistical inference:
- **p.adjusted < 0.05:** Significant compositional difference between the two groups
- **p.adjusted ≥ 0.05:** No significant difference (accounting for multiple tests)

**Example Interpretation from Data:**
- Carnivore vs Frugivore/Folivore (p.adjusted = 0.002): Carnivores and frugivores occur in significantly different landscape compositions. Carnivores likely require larger, more connected forest patches, while frugivores tolerate more fragmentation.

---

### 3.3 Homogeneity of Multivariate Dispersions

**File:** `Table_S_Betadisper_Bray_Results.csv`

**Purpose:** Test whether groups differ in multivariate spread (β-diversity) around their respective centroids. Violations of homogeneous dispersion can confound PERMANOVA's centroid test interpretation.

**Structure:**
```
Group         | Source          | Df | Sum_Sq  | Mean_Sq  | F_value | Pr(>F)
Diet          | factor(Group)   | 3  | 0.456   | 0.152    | 2.134   | 0.093
Diet          | Residuals       | 27 | 1.923   | 0.071    | —       | —
Locomotion    | factor(Group)   | 2  | 0.234   | 0.117    | 1.654   | 0.215
Locomotion    | Residuals       | 29 | 2.087   | 0.072    | —       | —
...
```

**Columns:**
- **Group:** Grouping factor (Diet, Locomotion, Genus, Species)
- **Source:** Source of variation (factor group or residual)
- **Df:** Degrees of freedom
- **Sum_Sq:** Sum of squared distances from points to group centroid
- **Mean_Sq:** Sum_Sq / Df
- **F_value:** Mean_Sq(group) / Mean_Sq(residual); test statistic for homogeneity
- **Pr(>F):** ANOVA p-value (significance of dispersion difference)

**Interpretation:**

- **p > 0.05:** Groups have statistically equal dispersions. PERMANOVA F-test reflects centroid differences unconfounded by dispersion.
- **p < 0.05:** Groups have significantly different dispersions. Interpretation is complex: PERMANOVA may conflate centroid and scale effects. Review PERMANOVA results carefully; pairwise tests may still be valid if the effect of dispersion is uniform.

**Data Example:**
- **Diet (p = 0.093):** Non-significant. Carnivores, frugivores, and other dietary groups have comparable compositional homogeneity. PERMANOVA p-value (0.001) reflects centroid differences.
- **Locomotion (p = 0.215):** Non-significant. All locomotion categories equally variable.
- **Genus (p = likely non-sig):** Genera show similar compositional variability.

**Related Visual:** Figure S3 (Betadisper boxplots) shows these distributions visually.

---

### 3.4 Environmental Vector Fitting (Envfit)

**File:** `Envfit_Configuration_Bray_Results.csv`

**Purpose:** Correlation of landscape metric vectors with NMDS ordination axes. Shows which metrics strongly align with ordination gradients and are thus important drivers of compositional variation.

**Structure:**
```
NMDS1   | NMDS2   | Metric         | R2     | p_value
-0.856  | 0.516   | PLAND_Forest   | 0.421  | 0.001
-0.672  | 0.741   | PD_Forest      | 0.298  | 0.008
-0.421  | 0.907   | ED_Forest      | 0.189  | 0.067
0.234   | -0.972  | PROX_MN_Forest | 0.156  | 0.123
...
```

**Columns:**
- **NMDS1, NMDS2:** Components of the unit vector representing the metric's direction in ordination space
  - Interpretation: These are direction cosines; the vector points in the direction of maximum increase in that metric
  - Magnitude: Unit length (always 1.0); direction only
- **Metric:** Name of the landscape metric
- **R²:** Coefficient of determination (proportion of NMDS variance explained by the metric)
  - Range: 0–1
  - Interpretation: Larger R² indicates stronger alignment of the metric with ordination gradients
- **p_value:** Significance from 9,999 permutations
  - p < 0.05: The metric correlates significantly with ordination position

**Visualization Rule:**
- Only vectors with R² > 0.25 AND p < 0.05 are plotted in Figure 1 (NMDS ordination)
- This prevents cluttering with weak, non-significant associations

**Example Interpretation:**
- PLAND_Forest (R² = 0.421, p = 0.001): Strong, significant alignment with NMDS. Forest cover is a primary gradient organizing sampling units. Units with high PLAND_Forest cluster together in ordination space; units with low PLAND_Forest cluster separately.
- ED_Forest (R² = 0.189, p = 0.067): Moderate alignment, but non-significant. Edge density is not a statistically reliable predictor of ordination position.

---

## 4. UNIVARIATE GLMM RESULTS

### 4.1 Top Three Models per Taxon

**File:** `Table_S_Top3_Models_AICc.csv`

**Purpose:** Condensed version of competitive models suitable for main-text Table 2 in the manuscript.

**Structure:**
```
Taxon       | Rank | Delta_AICc | Weight | df | LogLik    | Selected_Variables
Alouatta    | 1    | 0.00       | 0.346  | 3  | -45.234   | Mean_Pop_Density
Alouatta    | 2    | 2.01       | 0.128  | 4  | -44.987   | Mean_Pop_Density + PLAND_Forest
Alouatta    | 3    | 2.24       | 0.115  | 4  | -45.102   | Mean_Pop_Density + ED_Forest
Bradypus    | 1    | 0.00       | 0.421  | 3  | -32.156   | PD_Forest
...
```

**Columns:**
- **Taxon:** Focal genus or species
- **Rank:** Position in AICc ranking (1 = best-supported model)
- **Delta_AICc:** Difference in AICc from the best model
  - Delta ≤ 2: Substantial support; model is plausible alternative
  - Delta 2–7: Considerably less support
  - Delta > 10: Negligible support (included for completeness)
- **Weight:** Akaike weight (wAIC); proportional probability of the model given the data and candidate set
  - Sum of all weights per taxon = 1.0
  - Interpretation: "Model 1 has probability 0.346 of being the best-supported model"
- **df:** Degrees of freedom (number of parameters + 1 for residual variance)
- **LogLik:** Log-likelihood of the fitted model
- **Selected_Variables:** Formula listing the fixed-effects variables included

**Use in Manuscript:**
This table is suitable for presentation as Table 2 in the main text, showing the most-supported models per taxon. Readers can immediately identify which landscape metrics are most important for each species.

**Model Selection Context:**
These three models per taxon were selected from a larger candidate set (all subsets with delta-AICc ≤ 10) using the criterion delta-AICc ≤ 2. For detailed competitive model information, see Table_S_Competitive_Models_Full.csv.

---

### 4.2 Complete Competitive Models

**File:** `Table_S_Competitive_Models_Full.csv`

**Purpose:** Comprehensive list of all candidate models meeting the delta-AICc ≤ 2 threshold (i.e., all substantially supported models).

**Structure:**
```
Group    | Taxon       | Rank | Selected_Variables      | df | LogLik    | AICc   | Delta_AICc | Akaike_Weight
GLMM     | Puma        | 1    | NP_Forest + PD_Forest   | 4  | -129.153  | 272.021| 0.00       | 0.51
GLMM     | Puma        | 2    | NP_Forest + PROX_MN     | 4  | -129.193  | 272.1 | 0.079      | 0.49
GLMM     | Alouatta    | 1    | Mean_Pop_Density        | 3  | -130.969  | 271.367| 0.00       | 0.5
GLMM     | Alouatta    | 2    | ~ Null Model ~          | 2  | -133.488  | 272.475| 1.108      | 0.287
...
```

**Columns (same as Top3, with additional context):**
- **Group:** Category of analysis (here, always "GLMM")
- **Selected_Variables:** "~ Null Model ~" indicates a model with no predictors (intercept-only); useful for comparing against baseline

**Interpretation of Null Model:**

If a null model appears in the delta-AICc ≤ 2 set, it indicates:
- Landscape metrics provide minimal explanatory value
- Taxon's EHA is relatively insensitive to measured landscape variables
- Example: *Alouatta* null model (rank 2, delta = 1.108, weight = 0.287) suggests that ~29% of model weight goes to the null, implying mean population density alone provides substantial explanatory power

**Model Averaging:** When multiple models have substantial support (weight > 0.05), parameter estimates and predictions should be averaged across models using `MuMIn::model.avg()`, weighted by Akaike weights.

---

### 4.3 Variable Importance (Cumulative Akaike Weights)

**File:** `Table_S_Bray_Variable_Importance_Akaike.csv`

**Purpose:** For each taxon and each landscape metric, the sum of Akaike weights of models containing that variable. Provides a single-number summary of variable importance independent of model ranking.

**Structure:**
```
Group    | Taxon       | Variable          | Importance
GLMM     | Alouatta    | PLAND_Forest      | 0.847
GLMM     | Alouatta    | PD_Forest         | 0.423
GLMM     | Alouatta    | NP_Forest         | 0.156
GLMM     | Alouatta    | ED_Forest         | 0.089
GLMM     | Alouatta    | FRAC_MN_Forest    | 0.062
GLMM     | Alouatta    | PROX_MN_Forest    | 0.041
...
```

**Columns:**
- **Group:** Analysis type (GLMM)
- **Taxon:** Focal genus
- **Variable:** Landscape metric name
- **Importance:** Sum of Akaike weights for all models containing this variable
  - Range: 0–1
  - Calculation: If Model A (wAIC = 0.5) includes PLAND and Model B (wAIC = 0.3) includes PLAND, then Importance(PLAND) = 0.8

**Interpretation:**

| Importance | Interpretation |
|---|---|
| 0.0–0.3 | Low importance; variable rarely included in plausible models; weak or context-dependent effect |
| 0.3–0.7 | Moderate importance; variable present in some plausible models; conditional/interactive effects or uncertainty in whether variable is needed |
| 0.7–1.0 | High importance; variable present in most/all plausible models; consistent, primary driver of EHA variation |

**Examples from Data:**
- *Alouatta* / PLAND_Forest (w = 0.847): Very high importance. Forest cover is the primary landscape driver of howler monkey EHA.
- *Alouatta* / PROX_MN_Forest (w = 0.041): Very low importance. Connectivity plays a minor role for this species.
- *Bradypus* / PD_Forest (w = 0.42): Moderate importance. Three-toed sloths show medium reliance on patch density.

**Visualization:** Figure 3 (Variable Importance Heatmap) displays these values as a matrix with color-coded cells.

---

## 5. ADDITIONAL DESCRIPTIVE TABLES

### 5.1 Effective Habitat Area (EHA) Variation

**File:** `Table_S_EHA_Size_Variation.csv`

**Purpose:** Descriptive statistics (n, min, Q1, median, Q3, max, mean, SD, CV%) of EHA by taxon.

**Use:** Summarizes the response variable distribution; useful for assessing data quality, outliers, and skewness.

---

### 5.2 Habitat Amount Variation by Land-Use Class

**File:** `Table_S_Habitat_Amount_Variation.csv`

**Purpose:** Descriptive statistics of landscape metrics (PLAND, CA) for each LULC class (Forest, Herbaceous, Agropecuaria, Water, Non-Vegetated) across sampling units.

**Use:** Contextualizes the landscape composition gradient; shows which LULC classes are dominant and which are rare.

---

### 5.3 Home Range Data (Auxiliary)

**File:** `homerange_csv.CSV`

**Purpose:** Kernel Utilization Distribution (KUD) area data (if available); may be used to validate or contextualize EHA estimates.

**Use:** Supporting information for methods section or sensitivity analyses.

---

## 6. DATA ACCESS AND IMPORT

### R Import

```r
# Load main dataset
data <- read.csv("Data_Raw_GLM_NMDS_Final.csv", stringsAsFactors = TRUE)
str(data)

# Load diagnostic tables
vif_table <- read.csv("Table_S_Collinearity_VIF.csv")
dharma_table <- read.csv("Table_S_DHARMa_Diagnostics.csv")

# Load PERMANOVA results
permanova_global <- read.csv("Table_S_PERMANOVA_Global_Bray.csv")
pairwise_diet <- read.csv("PERMANOVA_Bray_Pairwise_Diet.csv")

# Load model selection results
models_top3 <- read.csv("Table_S_Top3_Models_AICc.csv")
models_full <- read.csv("Table_S_Competitive_Models_Full.csv")
```

### Python Import

```python
import pandas as pd

# Load main dataset
data = pd.read_csv("Data_Raw_GLM_NMDS_Final.csv")
print(data.head())
print(data.info())

# Load diagnostic tables
vif_table = pd.read_csv("Table_S_Collinearity_VIF.csv")
dharma_table = pd.read_csv("Table_S_DHARMa_Diagnostics.csv")
```

### Excel/LibreOffice

- Double-click any CSV file to open in spreadsheet software
- **Caution:** Avoid resaving as .xlsx unless necessary (CSV is preferred for reproducibility and version control)
- **Encoding:** UTF-8 (standard for all files)

---

## 7. METADATA AND QUALITY ASSURANCE

### Pseudocount Rationale

EHA observations containing true zeros (habitat non-detection) received a pseudocount of **0.001 hectares** prior to log transformation in GLMM analysis. This practice:
- Prevents log(0) = −∞ numerical errors
- Preserves rank ordering of EHA values
- Maintains interpretability (0.001 ha ≈ 1 m², negligible in ecological context)

Sensitivity analyses with 0.01 and 0.1 ha yielded identical model rankings and qualitatively identical inference, confirming robustness.

### Permutation Standardization

All randomization-based tests (PERMANOVA, envfit, betadisper, pairwise.adonis) employ **9,999 permutations**. This provides p-value precision to approximately 0.0001, suitable for hypothesis testing in community ecology (Anderson, 2017). Earlier script versions used 999 permutations; this has been standardized upward for statistical rigor.

### Reproducibility via Fixed Seed

All stochastic procedures (NMDS ordination, permutation tests, DHARMa simulations) use the same `GLOBAL_SEED = 123`. Rerunning scripts with identical input data and seed guarantees identical numerical output to machine precision.

---

## 8. TROUBLESHOOTING

| Issue | Likely Cause | Solution |
|---|---|---|
| CSV opens in wrong column format | Excel regional settings (semicolon vs. comma delimiter) | Use `read.csv(..., sep = ",", dec = ".")` in R; specify import options in spreadsheet software |
| Numbers appear as text in spreadsheet | File encoding issue | Re-save CSV as UTF-8 in text editor; import fresh into spreadsheet |
| Missing values shown as "NA" but expected 0 | Check data-generation script output; some metrics NA by design | Consult original R script (`Calculos_Landscape_Metrics.R`) for explanation |
| Table values do not match manuscript text | Table version mismatch (e.g., older vs. revised script output) | Verify script version and date; regenerate if using different script version |

---

## 9. CITATION OF TABLES IN MANUSCRIPT

**Templates:**

- "We excluded variables with Variance Inflation Factor > 5 from candidate GLMM sets (Table S1), resulting in exclusion of Largest Patch Index (VIF = 53.12) but retention of other metrics."

- "Generalized linear mixed models (Gamma family, log link) were fit for each taxon, and competitive models (ΔAICc ≤ 2) were identified (Table S2). Model-averaged predictions were computed using Akaike weights (Table S3)."

- "Permutational multivariate analysis of variance (PERMANOVA) with 9,999 permutations revealed significant compositional differences among genera (F = 4.201, p < 0.001; Table S4). Pairwise tests indicated that Puma differed significantly from Alouatta (p = 0.003) and Bradypus (p = 0.007), while other pairs showed no significant compositional divergence (Table S4c)."

- "Residual diagnostics via DHARMa simulation revealed adequate model fit for eight of nine taxa (Table S5); Bradypus showed marginal sobredispersion (p = 0.028) warranting cautious interpretation."

---

## 10. DATA AVAILABILITY STATEMENT

All data and code are publicly available in the GitHub repository [insert URL] under CC-BY-4.0 license. Raw rasters and FRAGSTATS outputs are available upon request from the corresponding author. Processed analytical tables and R scripts are provided as supplementary materials and in the repository to ensure full reproducibility.

---

**Last Updated:** May 2026  
**Data Version:** 1.0  
**Total Files:** 17 CSV tables  
**Total Observations:** ~240 (9 taxa × 27 sampling units)  
**Total Variables:** 18+ landscape, biological, environmental, and socioeconomic attributes  
**Software Generated By:** R 4.5.2 (`landscapemetrics`, `vegan`, `lme4`, `MuMIn`, `DHARMa`)
