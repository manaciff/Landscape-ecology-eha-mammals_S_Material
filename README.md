[README_GitHub.md](https://github.com/user-attachments/files/27899310/README_GitHub.md)
# Landscape composition and configuration as drivers of Effective Habitat Area for threatened mammals in the Brazilian Atlantic Forest

This repository hosts the R scripts and supplementary material associated with the manuscript submitted to *Landscape Ecology*, which evaluates how landscape composition and configuration influence the Effective Habitat Area (EHA) of nine threatened medium and large-sized mammal taxa in the Atlantic Forest of Rio de Janeiro State, Brazil.

---

## Overview

The Atlantic Forest is among the most biodiverse and severely threatened biomes worldwide, and its mammalian fauna has been particularly affected by habitat loss and fragmentation. This study integrates species occurrence data with landscape metrics derived from MapBiomas Collection 10.1 to quantify the influence of land-use composition and spatial configuration on the EHA of threatened mammals.

The EHA was estimated from the intersection between the Kernel Utilization Distribution (KUD) of each taxon and native forest cover, providing a refined surrogate for usable habitat at the landscape scale.

The analytical framework combines:

- Multivariate ordination through Non-metric Multidimensional Scaling (NMDS) based on Bray-Curtis dissimilarities.
- Permutational Multivariate Analysis of Variance (PERMANOVA) and multivariate homogeneity of group dispersions (betadisper).
- Environmental fitting (envfit) of landscape configuration metrics onto ordination space.
- Generalized Linear Mixed Models (GLMMs) with Gamma family and log link, followed by multimodel inference based on AICc and Akaike weights.

---

## Citation

If you use the scripts or supplementary data from this repository, please cite:

> [Author(s)] (in review). Landscape composition and configuration as drivers of Effective Habitat Area for threatened mammals in the Brazilian Atlantic Forest. *Landscape Ecology*.

A DOI for this repository can be obtained by archiving a release on [Zenodo](https://zenodo.org/), which is recommended to ensure long-term reproducibility.

---

## Repository structure

```
.
├── R/
│   ├── Script_LANDSCAPE_ECOLOGY_REVISED_v3_ColorPalettes.R
│   └── Collinearity_Correlation_MapBiomas.R
├── supplementary/
│   ├── Table_S_Collinearity_VIF.csv
│   ├── Table_S_PERMANOVA_Global_Bray.csv
│   ├── PERMANOVA_Bray_Pairwise_Species.csv
│   ├── PERMANOVA_Bray_Pairwise_Genus.csv
│   ├── PERMANOVA_Bray_Pairwise_Diet.csv
│   ├── PERMANOVA_Bray_Pairwise_Locomotion.csv
│   ├── Table_S_Betadisper_Bray_Results.csv
│   ├── Envfit_Configuration_Bray_Results.csv
│   ├── Table_S_Top3_Models_AICc.csv
│   ├── Table_S_Competitive_Models_Full.csv
│   ├── Table_S_Bray_Variable_Importance_Akaike.csv
│   └── Table_S_DHARMa_Diagnostics.csv
├── docs/
│   └── Justification_LULC_Aggregation_Analysis.docx
└── README.md
```

---

## Scripts

### `Script_LANDSCAPE_ECOLOGY_REVISED_v3_ColorPalettes.R`

Main analytical script. It implements the full workflow:

- Calculation of landscape composition metrics, including Percentage of Landscape (PLAND).
- Calculation of landscape configuration metrics, namely Patch Density (PD) and Number of Patches (NP). The Largest Patch Index (LPI) was evaluated and excluded due to collinearity (see Methods section of the manuscript).
- Multivariate analyses (NMDS, PERMANOVA, betadisper, and envfit) based on Bray-Curtis dissimilarities, with 9,999 permutations.
- Construction and selection of GLMMs (Gamma family, log link) using the `dredge` function from the `MuMIn` package, with multimodel inference based on AICc and Akaike weights.
- Residual diagnostics via the `DHARMa` package, with simulation outputs exported to disk.
- Generation of figures with consistent colour palettes.

The script includes `set.seed()` calls at the relevant stochastic steps to ensure reproducibility of permutation-based tests and ordination outputs.

### `Collinearity_Correlation_MapBiomas.R`

Auxiliary script for the assessment of multicollinearity among predictors derived from MapBiomas Collection 10.1. It computes pairwise Spearman correlations and Variance Inflation Factor (VIF) values, supporting the variable selection strategy adopted in the GLMM framework.

---

## Supplementary tables

| File | Content |
|------|---------|
| `Table_S_Collinearity_VIF.csv` | Variance Inflation Factor for predictor variables retained in the modelling framework. |
| `Table_S_PERMANOVA_Global_Bray.csv` | Global PERMANOVA results for Bray-Curtis dissimilarities across grouping factors. |
| `PERMANOVA_Bray_Pairwise_Species.csv` | Pairwise PERMANOVA contrasts among species, with Bonferroni-adjusted p-values. |
| `PERMANOVA_Bray_Pairwise_Genus.csv` | Pairwise PERMANOVA contrasts among genera. |
| `PERMANOVA_Bray_Pairwise_Diet.csv` | Pairwise PERMANOVA contrasts among dietary guilds. |
| `PERMANOVA_Bray_Pairwise_Locomotion.csv` | Pairwise PERMANOVA contrasts among locomotion modes. |
| `Table_S_Betadisper_Bray_Results.csv` | Multivariate homogeneity of group dispersions (betadisper) for each grouping factor. The Diet group violated the assumption of homogeneous dispersion, as discussed in the manuscript. |
| `Envfit_Configuration_Bray_Results.csv` | Environmental fitting of landscape configuration metrics onto the NMDS ordination space. |
| `Table_S_Top3_Models_AICc.csv` | Top three GLMMs per taxon, ranked by AICc. |
| `Table_S_Competitive_Models_Full.csv` | Full set of competitive GLMMs (ΔAICc ≤ 2) for each taxon, including selected variables, log-likelihoods, AICc values, ΔAICc, and Akaike weights. |
| `Table_S_Bray_Variable_Importance_Akaike.csv` | Variable importance derived from the sum of Akaike weights across the competitive model set. |
| `Table_S_DHARMa_Diagnostics.csv` | Residual diagnostics from DHARMa simulations, including dispersion, uniformity, and outlier tests. |

---

## Data sources

- **Land-use and land-cover data**: MapBiomas Collection 10.1 ([https://mapbiomas.org](https://mapbiomas.org)), reclassified into ecologically meaningful classes prior to metric calculation. The aggregation rationale is documented in `docs/Justification_LULC_Aggregation_Analysis.docx`.
- **Species occurrence data**: Compiled records of nine threatened medium and large-sized mammal taxa from the Atlantic Forest of Rio de Janeiro State. Detailed sources, filtering criteria, and KUD parameters are described in the Methods section of the manuscript.
- **Administrative boundaries**: Official cartographic base of Rio de Janeiro State.

Raw spatial data are not redistributed in this repository due to file size and licensing constraints. Links and DOIs to the original sources are provided in the manuscript.

---

## Software requirements

Analyses were performed in:

- **R version 4.5.2** (R Core Team).
- **ArcGIS Pro 3.1.0**, used for preliminary spatial processing.

The following R packages are required:

| Package | Purpose |
|---------|---------|
| `vegan` | NMDS, PERMANOVA, betadisper, envfit |
| `lme4` | Generalized Linear Mixed Models |
| `MuMIn` | Multimodel inference, AICc, Akaike weights |
| `DHARMa` | Residual diagnostics for mixed models |
| `landscapemetrics` | Calculation of landscape composition and configuration metrics |
| `ggplot2` | Production of figures |

Package versions used in the analyses are reported in the manuscript and can be retrieved via `sessionInfo()` after running the main script.

---

## Reproducibility

To reproduce the analyses:

1. Clone this repository.
2. Install the R packages listed above (compatible with R ≥ 4.5).
3. Place the input spatial layers in a local `data/` directory, following the structure described in the Methods section of the manuscript.
4. Run `Collinearity_Correlation_MapBiomas.R` to inspect predictor collinearity and confirm the variable subset used in the GLMM framework.
5. Run `Script_LANDSCAPE_ECOLOGY_REVISED_v3_ColorPalettes.R` to reproduce the multivariate analyses, GLMMs, residual diagnostics, and figures.

All file paths used in the scripts are defined relative to the project root, and no hardcoded absolute paths are required for execution.

---

## Terminology

The term **Effective Habitat Area (EHA)** is used consistently throughout the manuscript, scripts, and supplementary tables. It replaces the label "Area of Occupancy (AOO)" used in earlier versions of the project. EHA is defined as the intersection between the Kernel Utilization Distribution (KUD) of each taxon and native forest cover, and represents the portion of the species range that is effectively available as habitat.

Other relevant abbreviations used in the repository:

- **SUs**: Sampling Units (hexagonal grid cells used to summarise landscape metrics).
- **PLAND**: Percentage of Landscape.
- **PD**: Patch Density.
- **NP**: Number of Patches.
- **LPI**: Largest Patch Index (evaluated and excluded due to collinearity).
- **VIF**: Variance Inflation Factor.
- **AICc**: Akaike Information Criterion corrected for small sample sizes.

---

## Project context

This research is part of a broader effort to characterise the spatial dynamics of mammalian habitat in the Atlantic Forest of Rio de Janeiro State. Its outputs are intended to support evidence-based conservation planning and to inform environmental public policy in one of the most fragmented portions of the biome.

---

## License

The code in this repository is distributed under the [MIT License](LICENSE). Supplementary tables are made available for academic use, with attribution required. Third-party data retain the licenses of their original providers.

---

## Contact

For questions regarding the scripts, supplementary tables, or analytical decisions, please open an issue in this repository or contact the corresponding author of the manuscript.
