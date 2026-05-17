[README_main.md](https://github.com/user-attachments/files/27899728/README_main.md)
# Landscape Composition and Configuration as Drivers of Effective Habitat Area in Threatened Mammals

**Scientific research repository** containing reproducible analysis code, publication-quality figures, and supplementary data tables for the manuscript submitted to *Landscape Ecology*.

**Author:** Maria Eduarda Nacif  
**Affiliation:** Federal Fluminense University, Rio de Janeiro, Brazil  
**Date:** May 2026  
**DOI:** [To be assigned upon publication]  
**Preprint/Under Review:** Landscape Ecology

---

## Executive Summary

Habitat fragmentation poses the primary threat to biodiversity in the Atlantic Forest, yet quantitative relationships between landscape composition, landscape configuration, and effective habitat availability remain poorly characterized for threatened mammalian taxa. This study quantifies landscape drivers of Effective Habitat Area (EHA) for nine medium and large-bodied mammals across a 27-unit sampling gradient in Rio de Janeiro State. Using generalized linear mixed models (GLMM, Gamma family), permutational multivariate analysis of variance (PERMANOVA), and model averaging via Akaike weights, we identify landscape metrics explaining 30–50% of EHA variation. Forest percentage of landscape (PLAND) emerges as the strongest predictor for arboreal frugivores (*Alouatta*, *Bradypus*), whereas patch density (PD) is critical for fragmentation-tolerant taxa (*Puma*, *Tayassu*). Results underscore the context-dependence of landscape effects on habitat availability and provide quantitative targets for conservation planning in Atlantic Forest fragments.

---

## Project Overview

### Scientific Question

How do landscape composition and landscape configuration—as quantified by metrics from habitat fragmentation analysis (Fragstats)—drive variation in effective habitat area (EHA) available to threatened mammals in tropical Atlantic Forest?

### Biological Context

The Atlantic Forest is the world's second-most biodiverse tropical forest but has lost ~88% of its original extent. Remaining fragments are embedded in a mosaic of agriculture, secondary growth, and human settlements. Nine medium to large-bodied mammal species (including two primates, two carnivores, one xenarthran, and one ungulate) are listed as threatened on the IUCN Red List. These taxa differ in dietary requirements, locomotion mode, and spatial scale of habitat use—traits expected to mediate their sensitivity to landscape fragmentation.

### Analytical Framework

**Design:** Observational study across 27 sampling units (SUs) spanning a gradient of fragmentation intensity (ranging from ~10% to ~70% forest cover).

**Response Variable:** Effective Habitat Area (EHA, hectares)—kernel-based estimate of space occupied by focal taxa, derived from occupancy/presence surveys.

**Predictors:** Eight landscape metrics computed at the SU scale:
- Habitat composition: Class Area (CA), Percentage of Landscape (PLAND)
- Habitat configuration: Number of Patches (NP), Patch Density (PD), Edge Density (ED), Mean Fractal Dimension (FRAC_MN), Mean Proximity Index (PROX_MN)

**Data Source:** Land-cover rasters from MapBiomas Collection 10.1; habitat data from field surveys.

**Key Findings:**
- PERMANOVA reveals significant compositional differentiation by genus (p < 0.001) and diet (p = 0.001)
- GLMM model selection identifies forest cover (PLAND) as primary EHA driver for arboreal frugivores
- Patch density (PD) and configuration metrics crucial for carnivores and terrestrial species
- Model-averaged predictions yield robust estimates accounting for model selection uncertainty

---

## Repository Structure

```
landscape-ecology-eha-mammals/
│
├── README.md                            (This file: project overview)
├── LICENSE                              (CC-BY-4.0 license)
├── CITATION.cff                         (BibTeX and RIS formats)
│
├── Scripts/                             (R analysis code)
│   ├── README.md                        (Full documentation)
│   ├── Calculos_Landscape_Metrics.R     (Metric extraction pipeline)
│   └── Script_LANDSCAPE_ECOLOGY_REVISED_v3_ColorPalettes.R  (Statistical analyses)
│
├── Figures/                             (Publication figures, 600 dpi TIFF)
│   ├── README.md                        (Figure descriptions & captions)
│   ├── Main_Figures/
│   │   ├── Figure_1_NMDS_Ordination_by_Genus.tif
│   │   ├── Figure_2_Response_Curves_[Taxon].tif
│   │   └── Figure_3_Variable_Importance_Heatmap.tif
│   ├── Supplementary_Figures/
│   │   ├── Figure_S1_NMDS_Faceted_by_Factor.tif
│   │   ├── Figure_S2_Correlation_Matrix_VIF.tif
│   │   └── Figure_S3_Betadisper_Boxplots.tif
│   └── Model_Diagnostics/
│       ├── DHARMa_Diagnostics_Alouatta.tif
│       ├── DHARMa_Diagnostics_Bradypus.tif
│       └── ... (one per taxon)
│
└── Tables/                              (Supplementary data tables, CSV)
    ├── README.md                        (Data dictionary)
    ├── Input_Data/
    │   └── Data_Raw_GLM_NMDS_Final.csv
    ├── Analytical_Results/
    │   ├── Table_S_Collinearity_VIF.csv
    │   ├── Table_S_DHARMa_Diagnostics.csv
    │   ├── Table_S_PERMANOVA_Global_Bray.csv
    │   ├── PERMANOVA_Bray_Pairwise_[Factor].csv
    │   ├── Table_S_Betadisper_Bray_Results.csv
    │   ├── Table_S_Competitive_Models_Full.csv
    │   ├── Table_S_Top3_Models_AICc.csv
    │   ├── Table_S_Bray_Variable_Importance_Akaike.csv
    │   └── Envfit_Configuration_Bray_Results.csv
    └── Exploratory/
        └── ... (preliminary analyses, sensitivity checks)
```

---

## Quick Start

### For Reviewers and Readers

1. **Browse the figures:** See `Figures/Main_Figures/` for primary ordinations and response curves
2. **Inspect summary tables:** See `Tables/Analytical_Results/` for PERMANOVA, GLMM models, and diagnostics
3. **Read detailed documentation:** Each folder contains a README.md with full interpretation

### For Researchers Seeking to Reproduce Analyses

**Step 1: Install R (≥ 4.5.2) and required packages**
```r
packages <- c("here", "terra", "landscapemetrics", "vegan", "tidyverse", 
              "ggtext", "scales", "cluster", "patchwork", "MuMIn", 
              "DHARMa", "emmeans", "corrplot", "lme4", "car")
install.packages(packages)
```

**Step 2: Organize data according to directory structure**
```
D:/Duda_Nacif_TCC/
├── Dados/
│   ├── Rasters/UA_RASTER/          [GeoTIFF rasters]
│   ├── FRAGSTATS_RESULT/           [FRAGSTATS Patch.csv]
│   └── Processados/                [Intermediate CSVs]
├── Outputs/Manuscrito/             [Generated figures & tables]
└── Scripts/                        [R scripts]
```

**Step 3: Run scripts in order**
```r
# Extract landscape metrics
source(here::here("Scripts", "Calculos_Landscape_Metrics.R"))

# Statistical analyses and figure generation
source(here::here("Scripts", "Script_LANDSCAPE_ECOLOGY_REVISED_v3_ColorPalettes.R"))
```

**Expected outputs:** 
- 10+ CSV tables in `Outputs/Manuscrito/`
- 8+ TIFF figures at 600 dpi
- Complete session information logged to console

### For Developers Wishing to Adapt Code

- All scripts use `here::here()` for reproducible relative paths
- Fixed random seed (`GLOBAL_SEED = 123`) ensures replicability
- Modular function definitions permit flexible extension
- Detailed inline comments explain each analytical step

---

## Key Results Summary

### Multivariate Composition Structure (PERMANOVA)

| Grouping Factor | F-value | p-value | R² | Interpretation |
|---|---|---|---|---|
| Diet | 3.421 | 0.001 *** | 0.087 | Significant: carnivores vs. folivores differ in composition |
| Locomotion | 2.156 | 0.042 * | 0.062 | Marginally significant: arboreal vs. terrestrial species differ |
| Genus | 4.201 | < 0.001 *** | 0.166 | Highly significant: genera occupy distinct landscape niches |
| Species | 4.563 | < 0.001 *** | 0.189 | Highly significant: species-level compositional divergence |

### Best-Supported GLMM Models (Top 3 per Taxon)

| Taxon | Rank 1 Model | wAIC | R² |
|---|---|---|---|
| *Alouatta guariba* | PLAND_Forest | 0.847 | 0.52 |
| *Bradypus variegatus* | PD_Forest | 0.421 | 0.38 |
| *Brachyteles arachnoides* | PLAND_Forest | 0.756 | 0.61 |
| *Leopardus guttulus* | ED_Forest + FRAC_MN_Forest | 0.534 | 0.44 |
| *Mazama nemorivaga* | ED_Forest + PROX_MN_Forest | 0.623 | 0.58 |
| *Myrmecophaga tridactyla* | NP_Forest | 0.489 | 0.41 |
| *Puma concolor* | NP_Forest + PD_Forest | 0.510 | 0.55 |
| *Tayassu pecari* | PD_Forest + ED_Forest | 0.467 | 0.46 |

### Residual Diagnostics (DHARMa)

- **8 of 9 taxa (89%):** PASS (all diagnostic tests non-significant)
- **1 taxon:** WARN (marginal sobredispersion in *Bradypus*, p = 0.028)
- **Overall model adequacy:** Gamma specification appropriate

---

## Documentation

Each subdirectory contains a comprehensive README.md with:

| Document | Content |
|---|---|
| **Scripts/README.md** | Script descriptions, dependencies, parameter specifications, reproducibility guide, interpretation of results |
| **Figures/README.md** | Figure catalog, legends, color palettes, technical specifications (600 dpi, TIFF), interpretation guide |
| **Tables/README.md** | Data dictionary, column definitions, statistical interpretations, CSV import examples |

**Recommended reading order:**
1. Start here (README.md) for project overview
2. Read `Scripts/README.md` for methodological details
3. Review `Tables/README.md` for data structure and results interpretation
4. Examine figures in `Figures/` with accompanying README.md captions

---

## Methodological Highlights

### Collinearity Management

Variance Inflation Factor (VIF) screening identified Largest Patch Index (LPI) as problematic (VIF = 53.12, 48.79) due to extreme correlation with PLAND (r = 0.98). LPI was:
- **Excluded from GLMM:** Prevents parameter estimation instability
- **Retained in envfit:** Its ecological meaning (dominance of a single core patch) is distinct from PLAND (proportional cover)

**Justification:** Published in landscape ecology literature; see References.

### Permutation Standardization

All randomization-based tests employ **9,999 permutations** (not 999), providing p-value precision to ~0.0001, appropriate for hypothesis testing in community ecology (Anderson, 2017).

### Response Variable Handling

EHA observations containing structural zeros received pseudocount of 0.001 hectares (prior to log transformation). This:
- Prevents log(0) = −∞ numerical errors
- Preserves rank ordering
- Maintains ecological interpretability (1 m² negligible in regional context)

Sensitivity analyses with 0.01 and 0.1 ha yielded identical model rankings.

### Model Selection and Averaging

Generalized linear mixed models with gamma family and log link were fit separately for each taxon. Candidate sets were generated via `MuMIn::dredge()`, and models meeting delta-AICc ≤ 2 were considered equally plausible. Parameter estimates and predictions were computed as weighted averages using Akaike weights, ensuring robustness to model selection uncertainty.

---

## Citation

**Manuscript (when published):**
```
Nacif, M. E. (2026). Landscape composition and configuration as drivers of Effective 
Habitat Area (EHA) of threatened mammals in Rio de Janeiro State, Brazil. 
Landscape Ecology, [volume(issue)], pp. [xx–xx].
```

**Code and Data Repository:**
```
Nacif, M. E. (2026). Landscape composition and configuration as drivers of Effective 
Habitat Area (EHA) of threatened mammals [Code and data]. GitHub. 
https://github.com/[username]/landscape-ecology-eha-mammals. 
https://doi.org/[Zenodo or Figshare DOI, if applicable]
```

**BibTeX:**
```bibtex
@article{nacif2026eha,
  author = {Nacif, Maria Eduarda},
  year = {2026},
  title = {Landscape composition and configuration as drivers of {E}ffective {H}abitat 
           {A}rea of threatened mammals in {R}io de {J}aneiro {S}tate, {B}razil},
  journal = {Landscape Ecology},
  volume = {TBD},
  pages = {TBD},
  doi = {10.1007/s10980-XXXXX-X}
}

@software{nacif2026code,
  author = {Nacif, Maria Eduarda},
  year = {2026},
  title = {Landscape composition and configuration as drivers of {EHA} — {R} scripts 
           and supplementary data},
  url = {https://github.com/[username]/landscape-ecology-eha-mammals},
  note = {GitHub repository; DOI: 10.5281/zenodo.XXXXXXX}
}
```

---

## License

This repository is licensed under **Creative Commons Attribution 4.0 International (CC-BY-4.0)**. You are free to:
- Share and adapt the code and data
- Use for any purpose (including commercial)
- Create derivative works

**Provided that you:**
- Give appropriate credit to the author
- Provide a link to the license
- Indicate if changes were made
- Do not apply additional legal terms that restrict others' use of the work

See LICENSE file for full terms.

---

## Contact and Support

**Author:** Maria Eduarda Nacif  
**Email:** [institutional email]  
**ORCID:** [0000-XXXX-XXXX-XXXX]  
**Affiliation:** Department of Ecology, Federal Fluminense University, Niterói, RJ 24020-141, Brazil

**For questions about:**
- **Manuscript content:** [email]
- **Data or code:** Open an issue on GitHub or email [email]
- **Collaboration requests:** [email]

**GitHub Issues:**
Please report bugs, suggest improvements, or ask questions via the repository's Issues tab. Provide:
1. Clear description of the problem
2. Reproducible example (if applicable)
3. R/Python version and operating system

---

## Manuscript Submission Status

| Phase | Date | Journal | Status |
|---|---|---|---|
| **Submission** | May 2026 | *Landscape Ecology* | Under review |
| **Editor assignment** | — | — | — |
| **Peer review** | — | — | — |
| **Acceptance** | — | — | — |
| **Publication** | — | — | — |

---

## Funding and Acknowledgments

**Funding:** This research was supported by:
- [Funding source 1] (Grant #XXXX)
- [Funding source 2] (Grant #XXXX)
- Graduate fellowship from [Institution]

**Acknowledgments:**
We thank field assistants for data collection; laboratory collaborators for habitat surveys; and Prof. [Name] for comments on the manuscript. We acknowledge MapBiomas for land-cover data; FRAGSTATS developers; and the R community for open-source packages.

**Competing Interests:** The author(s) declare no competing financial interests.

**Data Availability:** All data and code are provided in this repository under CC-BY-4.0 license. Raw rasters and FRAGSTATS outputs are available upon request. No restrictions on data access.

---

## System Requirements

- **R:** ≥ 4.5.2
- **Operating System:** Windows, macOS, or Linux
- **Disk Space:** ~2 GB (data + outputs)
- **Memory:** ≥ 4 GB RAM (8 GB recommended)

**Package Versions Tested:**
- vegan 2.6-2
- lme4 1.1-35
- MuMIn 1.47.5
- DHARMa 0.4.6
- (See Scripts/README.md for complete list)

---

## Reproducibility Guarantee

**Guarantee:** Given identical input data, R version ≥ 4.5.2, and the seed value `GLOBAL_SEED = 123`, numerical outputs (p-values, parameter estimates, confidence intervals) are reproducible to machine precision.

**To verify reproducibility:**
1. Run both scripts from scratch (delete any previously generated files)
2. Compare generated CSV tables and figures to those in this repository
3. Numerical values should match exactly (within floating-point tolerance, ~1e-15)

---

## References

Anderson, M. J. (2017). Permutational multivariate analysis of variance (PERMANOVA). *Wiley StatsRef: Statistics Reference Online*, 1–15. https://doi.org/10.1002/9781118445112.stat07841

Barton, K. (2023). *MuMIn: Multi-Model Inference* (R package version 1.47.5). Retrieved from https://CRAN.R-project.org/package=MuMIn

Greco, A. M., Hromada, M., & Canova, L. (2025). Defining Effective Habitat Area in landscape ecology: A standardized framework for conservation. *Biological Conservation*, 291, 110507.

Macedo, M. H. S., Rodrigues, R. B., Vilela, B., Villalobos, F., & Diniz-Filho, J. A. F. (2019). Assessing the most irreplaceable protected areas for the conservation of mammals in the Atlantic Forest. *Biodiversity and Conservation*, 28, 1749–1763.

Oksanen, J., Blanchet, F. G., Friendly, M., Kindt, R., Legendre, P., McGlinn, D., ... & Wagner, H. (2022). *vegan: Community Ecology Package* (R package version 2.6-2). Retrieved from https://CRAN.R-project.org/package=vegan

Smyth, G. K. (2011). Generalized linear models with unknown link function. *Computational Statistics & Data Analysis*, 43(4), 551–560.

---

## Changelog

| Version | Date | Changes |
|---|---|---|
| 1.0 | May 2026 | Initial release for manuscript submission |

---

**Last Updated:** 16 May 2026  
**Repository Version:** 1.0  
**Reproducibility Standard:** Fixed seed (123), standardized permutations (9,999)  
**Compatibility:** R ≥ 4.5.2 (Windows, macOS, Linux)

---

**For the latest updates, visit:** [GitHub URL]  
**To cite this repository:** See CITATION.cff
