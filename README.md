# Tissue tropism of total mercury accumulation in an aerial insectivore, the Purple Martin (*Progne subis*)

Data and analysis code accompanying the manuscript of the same title.

**Author:** Victoria A. Wiley
**Status:** Submitted to *Environmental Toxicology and Chemistry*

---

## Overview

This repository contains the R code used to analyze total mercury (THg) concentrations across seven internal tissues, seven feather types, and whole-body homogenates in 56 Purple Martins (*Progne subis*) collected in Erie, Pennsylvania, USA (2022–2025).

The analysis addresses five questions:

1. How is THg partitioned among internal tissues (tissue tropism)?
2. Do THg concentrations differ by age class or sex?
3. Which internal tissues best predict whole-body THg burden?
4. How well do feathers serve as non-lethal proxies for internal THg?
5. How do blood-equivalent risk estimates compare across source tissues?

---

## Repository structure

```
.
├── README.md
├── code/
│   ├── 01_tissue_tropism_analysis.R    # Main analysis and figures
│   └── 02_blood_equivalent_conversion.R # Blood-equivalent THg conversion
├── data/
│   ├── all_data_wide_with_wb_10rmvd_use.csv  # Internal tissue THg (µg/g dw)
│   └── feather_data_updated.csv              # Feather THg (µg/g dw)
└── output/
    ├── Table_1_Combined_Tissues.csv
    └── figures/
```

---

## Data

All THg concentrations are reported in µg/g dry weight, corrected for extraction efficiency of the respective analytical run.

### `all_data_wide_with_wb_10rmvd_use.csv`

One row per individual.

| Column | Description |
|---|---|
| `bird_id` | Unique individual identifier |
| `age` | Age class: `asy` (adult, after second year) or `sy` (subadult, second year) |
| `sex` | `M` or `F` |
| `brain_ug_g` | Brain THg (µg/g dw) |
| `heart_ug_g` | Heart THg |
| `lung_ug_g` | Lung THg |
| `liver_ug_g` | Liver THg |
| `pec_ug_g` | Pectoralis muscle THg |
| `gas_ug_g` | Gastrocnemius muscle THg |
| `gi_ug_g` | Gastrointestinal tract THg |
| `wb_ug_g` | Whole-body homogenate THg |

### `feather_data_updated.csv`

One row per individual.

| Column | Description |
|---|---|
| `bird_id` | Unique individual identifier (joins to tissue data) |
| `age`, `sex` | As above |
| `pr` / `pl` | Right / left first primary feather THg |
| `rr` / `rl` | Right / left third rectrix feather THg |
| `crown` | Crown feather THg |
| `breast` | Breast feather THg |
| `dorsal` | Dorsal feather THg |

Missing values (`NA`) indicate samples with insufficient mass for analysis (< 5 mg) or tissues not recovered during dissection.

---

## Requirements

Analyses were conducted in **R version 4.3.1**.

```r
install.packages(c(
  "tidyverse",     # Data manipulation and ggplot2
  "lme4",          # Linear mixed-effects models
  "lmerTest",      # p-values for lme4 models
  "emmeans",       # Estimated marginal means, post-hoc comparisons
  "multcomp",      # Compact letter displays
  "multcompView",
  "performance",   # Model diagnostics
  "corrplot",      # Correlation heatmaps
  "patchwork"      # Figure assembly
))
```

---

## Analysis workflow

`code/01_tissue_tropism_analysis.R` runs in sequence:

| Section | Contents |
|---|---|
| 1 | Data import; conversion to long format |
| 2 | Assumption checks (Shapiro-Wilk, Bartlett's test; raw vs. log10) |
| 3 | Demographic effects — linear mixed models with tissue × age and tissue × sex interactions, `bird_id` as random effect |
| 4 | Tissue tropism comparisons by age class; Tukey post-hoc; **Figure 1** (boxplot with compact letter display) |
| 5 | Simple linear regressions of organ THg against whole-body THg; **Figure 2** (faceted, ordered by R²) |
| 6 | **Figure 4** — violin plot comparing whole body, organ average, and feather average |
| 7 | Pearson correlation matrix across all tissues; **Figure 3** (corrplot); extraction of best feather proxy per organ |
| 8 | Z-score heatmaps for individual-level variation in organs and feathers |
| 9 | Summary statistics export (**Table 1**) |

`code/02_blood_equivalent_conversion.R` converts pectoralis, liver, and breast feather THg to blood-equivalent THg (µg/g wet weight) using equations compiled by Ackerman et al. (2016), and tabulates the number of individuals exceeding published avian effect concentrations (**Table 3**).

### Reproducing the analysis

File paths in the scripts are currently absolute. To run locally, either set the working directory to the repository root and change paths to relative form, or edit the paths at the top of each script.

```r
setwd("path/to/repository")
source("code/01_tissue_tropism_analysis.R")
source("code/02_blood_equivalent_conversion.R")
```

---

## Notes on the analysis

**Untransformed data.** Raw THg concentrations violated assumptions of normality and homogeneity of variance, and log10 transformation did not improve residual distributions. Because linear mixed-effects models are robust to non-normality at adequate sample sizes (Schielzeth et al., 2020), and residual diagnostics favored the untransformed data, all models use untransformed concentrations.

**Blood-equivalent conversion.** Conversion equations were derived from non-passerine species. Converted values are approximations intended for comparison against published effect concentrations, not predicted blood concentrations. The feather–blood relationship explains substantially less variance (R² = 0.32) than the muscle–blood (R² = 0.90) or liver–blood (R² = 0.88) relationships.

**Age classes.** `asy` = after second year (adult); `sy` = second year (subadult, approximately one year old at death). Separate models were fit by age class following significant tissue × age interactions.

---

## Citation

If you use this code or data, please cite the manuscript:

> Wiley, V.A., et al. Tissue tropism of total mercury accumulation in an aerial insectivore, the Purple Martin (*Progne subis*). [Journal, year, DOI to be added upon publication]

---

## Key references

Ackerman, J.T., Eagles-Smith, C.A., Herzog, M.P., Hartman, C.A., Peterson, S.H., Evers, D.C., Jackson, A.K., Elliott, J.E., Vander Pol, S.S., Bryan, C.E. (2016). Avian mercury exposure and toxicological risk across western North America: A synthesis. *Science of the Total Environment*, 568, 749–769.

Eagles-Smith, C.A., Ackerman, J.T., Adelsbach, T.L., Takekawa, J.Y., Miles, A.K., Keister, R.A. (2008). Mercury correlations among six tissues for four waterbird species breeding in San Francisco Bay, California, USA. *Environmental Toxicology and Chemistry*, 27(10), 2136–2153.

Schielzeth, H., Dingemanse, N.J., Nakagawa, S., Westneat, D.F., Allegue, H., Teplitsky, C., Réale, D., Dochtermann, N.A., Garamszegi, L.Z., Araya-Ajoy, Y.G. (2020). Robustness of linear mixed-effects models to violations of distributional assumptions. *Methods in Ecology and Evolution*, 11(9), 1141–1152.

---

## Contact

Victoria A. Wiley — vaw33@nau.edu
Department of Biological Sciences
Northern Arizona University

## License

[Choose one — e.g. MIT for code, CC BY 4.0 for data.]
