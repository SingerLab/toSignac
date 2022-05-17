
<!-- README.md is generated from README.Rmd. Please edit that file -->

## GAC: Genetic Analysis of Cells

------------------------------------------------------------------------

<!-- badges: start -->

[![Lifecycle:
maturing](https://img.shields.io/badge/lifecycle-maturing-blue.svg)](https://www.tidyverse.org/lifecycle/#maturing)
[![R build
status](https://github.com/r-lib/usethis/workflows/R-CMD-check/badge.svg)](https://github.com/r-lib/usethis/actions)
[![Codecov test
coverage](https://codecov.io/gh/r-lib/usethis/branch/master/graph/badge.svg)](https://codecov.io/gh/SingerLab/gac?branch=master)
[![Travis build
status](https://travis-ci.com/SingerLab/gac.svg?branch=master)](https://travis-ci.com/SingerLab/gac)
[![CRAN
status](https://www.r-pkg.org/badges/version/gac)](https://CRAN.R-project.org/package=gac)
<!-- badges: end -->

# GAC is currently in ALPHA-release

The goal of GAC is to deliver a formal end-to-end analysis by
integrating proven methods of quantitative genetics, statistics, and
evolutionary biology for the genetic analysis of single-cell DNA copy
number. GAC implements a simple, lightweight, and open-source R
framework (Figure 1). Inspired, but unlike Seurat and Scanpy, adapts the
logic of espressioSet/AnnData into relational matrices in native R which facilitate the
integration of algorithms for the downstream analysis of single-cell DNA
data wich is so desperately needed. 

GAC facilitates the downstream analyses of segmented data with common segments by
concurrently managing the X, Y across all cells or samples e.g. the
output of [Varbin/Ginkgo](https://github.com/robertaboukhalil/ginkgo),
[FACETS](https://github.com/mskcc/facets), [MUMdex](http://mumdex.com),
[HMMcopy](https://bioconductor.org/packages/release/bioc/html/HMMcopy.html),
or [SCOPE](https://github.com/rujinwang/SCOPE). The unsegmented bin read
counts is *not a correct* input. GAC uses
[ComplexHeatmap](https://jokergoo.github.io/ComplexHeatmap-reference/book/),
an ultra-powerful tool for heatmaps to help visualize the data.

To implement GAC we require five easy to generate inputs: - a copy
number / genotype matrix (X) (bins\[i\] x cells\[j\]) - a phenotype
matrix (Y) (cells\[j\] x phenotype\[*y*\]) - a qc matrix (technical
wet-lab notes) (qc) (cells\[j\] x qc\[*c*\]) - a gene to bin index
(gene.index) - the genomic coordinates of the bins or genotypes
(chromInfo) - and an optional expression matrix (Ye; for DNA-RNA or
same-cell G+T([Macaulay, et
al.2015](https://dx.doi.org/10.1038/nmeth.3370))

![Figure 1.](figures/CNR.png)

## Installation

### Dependencies:

-   [ComplexHeatmap](https://jokergoo.github.io/ComplexHeatmap-reference/book/)
-   [vegan](https://github.com/vegandevs/vegan)
-   [dplyr](https://dplyr.tidyverse.org)
-   [SCclust](https://github.com/KrasnitzLab/SCclust)

### Install dependencies
```
install.packages("devtools")
devtools::install_github("KrasnitzLab/SCclust")

install.packages("BiocManager")
BiocManager::install(c("ComplexHeatmap", "ConsensusClusterPlus"))
```

You can install the development version from
[GitHub](https://github.com/) with:

```
devtools::install_github("SingerLab/gac")
```

## Examples

This is a basic example for drawing a copy number heatmap. For a
comprehensive overview of the package please follow the
`getting_started.Rmd` in the `vignettes/`

``` r
library(gac)

## basic example code
data(cnr)
data(segCol)
data(legSeg)

( excl.cells <- rownames(cnr$qc)[cnr$qc$qc.status == "FAIL"] )
#> [1] "cell5"  "cell11"

cnr <- excludeCells(cnr, excl = excl.cells)

aH <- HeatmapCNR(cnr, what = 'X', col = segCol, show_heatmap_legend = FALSE)

draw(aH, annotation_legend_list = list(legSeg))
```

<img src="man/figures/README-example-1.png" width="100%" />

``` r
bH <- HeatmapCNR(cnr, what = "genes",
                 which.genes = c("CDK4", "MDM2"),
                 col = segCol, show_heatmap_legend = FALSE)
#> Warning: The input is a data frame, convert it to the matrix.

draw(bH, annotation_legend_list = list(legSeg))
```

<img src="man/figures/README-example-2.png" width="100%" />

### Motivation and design

-   This package came out of the need to deliver some results. During
    the 11th hour (more like in borrowed time), I saw I was spending 85%
    of my time keeping 3 tables syncornized (bins, genes, and
    phenotypes), 10% rendering heatmaps, and 5% actually looking at the
    results. I began to think how lucky the people who only work with
    single-cell RNAseq are to have tools like Seurat and Scanpy, how
    simple and flexible those two tools are, and how nothing for DNA
    copy number is as powerful as the sister tools Seurat and Scanpy to
    manage the copy number matrix. I eventually realized that the main
    diference is the restriction imposed by the genome coordinates.
    While staring at the AnnData diagram I realized that for copy number
    data, the unit is a `bin` and the .X should be a matrix of common
    `bins` for all cells. However, to make biological sense of the data,
    **gene level resolution** is required. Thus, building a syncronized
    matrix with genes is of outmost importance. At the 11th hour, having
    a gene to bin index (gene.index) allowed the flexibility to
    interpolate the bin data to gene level resolution and integration to
    the complete set of phenotypes, and QC data, but it’s not the
    restricted to the mouse mouse or human genomes. Cows, viruses, and
    plants have genomes too!

The Singer Lab single-cell wet-lab and dry-lab endevours are carried
forward by a skeleton crew. The need to have something simple that can
help reduce the 85% of the time spent syncronizing bins, to genes, to
phenotypes, and QC matrices capable of handling a large data set of
&gt;24,000 cells was greatly needed. Knowing the data is growing by the
week, I integrated functions to deal with the n+1 problem. Lastly, my
background in animal genomics allowed me to borrow the succesful
frameworks used in Genomic Selection in an abstract way in hopes that we
can provide appropriate models for future same-cell technologies.

We hope you enjoy !

-   Rodrigo, et al.

# What’s in the works

-   Integration of Henderson’s Animal Model for the phenoype-genotype
    analyses

-   Integration with MLR for non-linear genetic models

-   Integration with CORE and GISTIC2 for fidning focal and recurrent
    events

-   Integration of infScite for somatic alteration evolution

-   Integration with Pathview for KEGG pathway visualization

-   support for .seg files

-   Cleaner code with tidyverse

-   CRAN testing

# Licence

GAC framework and code is distributed under a BSD-3 License
