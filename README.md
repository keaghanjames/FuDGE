# FuDGE

Code and data for *FuDGE: a Functional Distinctiveness and Global Endangerment score for birds*.

This repository contains the complete analysis pipeline used to compute Functional Distinctiveness (FuD) and the ecoDGE/FuDGE conservation-priority index for birds, compare them against their evolutionary equivalents (ED and EDGE), and test their efficiency in protecting phylogenetic and functional diversity under simulated extinction scenarios.

---

## Repository Contents

### Analysis Script

| File | Description |
|------|-------------|
| `FuDGE_clean.Rmd` | Main R Markdown script. Runs the full analysis from data loading through to all figures and model outputs. Sections cover tree rescaling, computation of FuD and ED, PGLS modelling, clade-level analyses, FuDGE/EDGE comparison, extinction simulations, and conservation-prioritisation efficiency curves. |
| `FuDGE_clean.html` | Pre-compiled HTML output of `FuDGE_clean.Rmd` with all results and figures rendered inline. |

---

### Input Data — Previously Published Sources

The five files below are derived from previously published datasets. Placeholder files are provided in this repository; download the originals from the sources linked in each file and place them in this directory before running the Rmd script.

| File | Description |
|------|-------------|
| `UPGMA_functional_tree_birds.tree` | Functional phylogram for birds constructed via UPGMA clustering of morphological trait distances (Skeels & Yaxley 2023). Used to calculate Functional Distinctiveness (FuD) scores via `evol.distinct()`. |
| `singe_bird_phylo.tre` | A single molecular time-calibrated consensus phylogeny for birds (Jetz et al. 2012; BirdTree). Used to calculate Evolutionary Distinctiveness (ED) and as the covariance structure in PGLS models. |
| `AVONET1_BirdLife.csv` | AVONET morphological and taxonomic database for birds (Tobias et al. 2022). Columns used: `Species1`, `Family1`, `Order1`. Provides order-level taxonomy for clade analyses. |
| `BirdLife-BirdTree crosswalk.csv` | Species-name crosswalk linking BirdLife (`Species1`) and BirdTree (`Species3`) taxonomies. Used to reconcile species names between the IUCN assessments and the phylogeny. |
| `assessments.csv` | IUCN Red List species assessments. Columns used: `scientificName`, `redlistCategory`. Provides threat-status data used to compute the Global Endangerment (GE) score. |

---

### Generated Data

These files are produced by running `FuDGE_clean.Rmd` and are included here for convenience.

| File | Description |
|------|-------------|
| `complete_data.csv` | Master species-level dataset containing ED, FuD, log-transformed scores, EDGE, FuDGE, taxonomic order and family, IUCN threat category (GE score), and extinction probabilities for all birds present in both the phylogeny and the morphological dataset. |
| `order_coefsGLS.csv` | Per-order GLS slope estimates (and 95% confidence intervals) for the relationship ln(FuD) ~ ln(ED), without phylogenetic correction. Passeriformes is the reference level. |
| `order_coefsPGLS.csv` | Per-order PGLS slope estimates (and 95% confidence intervals) for the relationship ln(FuD) ~ ln(ED), with Brownian-motion phylogenetic correction. |

---

### Pre-fitted Model Objects

PGLS models are computationally expensive (hours of run time). Pre-fitted model objects are provided so the Rmd can be run without refitting them. Each can be recreated using the commented-out code in the relevant section of `FuDGE_clean.Rmd`.

| File | Description |
|------|-------------|
| `pglsModel.Rdata` | Global PGLS model: `log(FuD) ~ log(ED)`, Brownian-motion correlation structure across the trimmed molecular phylogeny (`phy_trim`), fit by maximum likelihood. |
| `pglsModel_clade.rds` | PGLS clade model: `log(FuD) ~ log(ED) × Order`, with interaction terms for every bird order (Passeriformes as reference level). Used to test whether the ED–FuD slope varies across clades. |
| `pglsFuDGE.Rdata` | PGLS model: `scale(FuDGE) ~ scale(EDGE)`. Used to quantify the correlation between the two composite conservation-priority indices while accounting for shared evolutionary history. |
| `pglsED_End.Rdata` | PGLS model: `GE ~ log(ED)`. Tests whether evolutionary distinctiveness predicts IUCN threat status above and beyond phylogenetic covariance. |
| `pglsFuD_End.Rdata` | PGLS model: `GE ~ log(FuD)`. Tests whether functional distinctiveness predicts IUCN threat status above and beyond phylogenetic covariance. |

---

## Dependencies

```r
install.packages(c(
  "phytools", "picante", "geiger", "nlme",
  "tidyr", "dplyr", "ggplot2", "ggpubr",
  "ggh4x", "gghalves", "caret", "broom", "viridis"
))
```

---

## Notes

- The molecular phylogeny file is named `singe_bird_phylo.tre` (a typo for "single") throughout the script; this filename is used as-is to maintain consistency with the analysis code.
- The functional tree is rescaled to match the total depth of the molecular phylogeny before computing FuD scores, so that ED and FuD are on a directly comparable scale.
