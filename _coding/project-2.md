---
title: "10x Genomics: identify cells subsets"
excerpt: "Identify B cell subsets for BCR analysis"
collection: coding
tags:
  - R
  - Bioinformatics
---

Compiled: February 05, 2020

-   [Goal](#goal)
-   [Load and process the all CD19 run](#load-and-process-the-all-cd19-run)
    -   [Load the Seurat object](#load-the-seurat-object)
    -   [Standard pre-processing workflow](#standard-pre-processing-workflow)
    -   [Normalizing the data](#normalizing-the-data)
    -   [Identification of highly variable features (feature selection)](#identification-of-highly-variable-features-feature-selection)
    -   [Scaling the data](#scaling-the-data)
    -   [Perform linear dimensional reduction](#perform-linear-dimensional-reduction)
    -   [Cluster the cells](#cluster-the-cells)
    -   [Run non-linear dimensional reduction (UMAP/tSNE)](#run-non-linear-dimensional-reduction-umaptsne)
-   [Locate subsets](#locate-subsets)
    -   [Identify markers for all subsets](#identify-markers-for-all-subsets)
-   [Manually ID subset](#manually-id-subset)

------------------------------------------------------------------------

Goal
====

Identify B cell subsets using known markers to extract cell barcodes. The barcodes will be used to access the related immunoglobulins sequences in the 5' sequencing dataset.

Load and process the all CD19 run
=================================

``` r
# Load libraries
library(ggplot2)
library(cowplot)
library(Matrix)
library(dplyr)
library(Seurat)
library(plotly) #to generate interactive graph
library(data.table) #to transpose the data frame
```

Run the [Seurat pipeline](https://satijalab.org/seurat/v3.1/pbmc3k_tutorial.html).

Load the Seurat object
----------------------

``` r
# Load the all CD19 dataset
cd19.data <- Read10X(data.dir = "Data/Sample1_CD19+_totalBcells/filtered_gene_bc_matrices/GRCh38/")
# Initialize the Seurat object with the raw (non-normalized data).
cd19 <- CreateSeuratObject(counts = cd19.data, project = "CD19", min.cells = 3, min.features = 200)
```

    ## Warning: Feature names cannot have underscores ('_'), replacing with dashes
    ## ('-')

``` r
cd19
```

    ## An object of class Seurat 
    ## 15294 features across 4178 samples within 1 assay 
    ## Active assay: RNA (15294 features)

Standard pre-processing workflow
--------------------------------

``` r
cd19[["percent.mt"]] <- PercentageFeatureSet(object = cd19, pattern = "^MT-")

#We filter cells that have unique feature counts over 2,500 or less than 200
#We filter cells that have >5% mitochondrial counts

cd19 <- subset(x = cd19, subset = nFeature_RNA > 200 & nFeature_RNA < 2500 & percent.mt < 5)
```

Normalizing the data
--------------------

``` r
cd19 <- NormalizeData(object = cd19, normalization.method = "LogNormalize", scale.factor = 1e4)
```

``` r
cd19 <- FindVariableFeatures(cd19)
```

Identification of highly variable features (feature selection)
--------------------------------------------------------------

``` r
cd19 <- FindVariableFeatures(object = cd19,selection.method = 'vst', nfeatures = 2000)
```

Scaling the data
----------------

``` r
# Next, we apply a linear transformation ('scaling') that is a standard pre-processing step prior to dimensional reduction techniques like PCA. The `ScaleData` function:
# 
# * Shifts the expression of each gene, so that the mean expression across cells is 0
# * Scales the expression of each gene, so that the variance across cells is 1
#     + This step gives equal weight in downstream analyses, so that highly-expressed genes do not dominate
# * The results of this are stored in `pbmc[["RNA"]]@scale.data`

all.genes <- rownames(x = cd19)
cd19 <- ScaleData(object = cd19, features = all.genes)
```

    ## Centering and scaling data matrix

Perform linear dimensional reduction
------------------------------------

``` r
cd19 <- RunPCA(cd19, verbose = FALSE)
```

Cluster the cells
-----------------

``` r
cd19 <- FindNeighbors(object = cd19, dims = 1:10)
```

    ## Computing nearest neighbor graph

    ## Computing SNN

``` r
cd19 <- FindClusters(object = cd19, resolution = 0.5)
```

    ## Modularity Optimizer version 1.3.0 by Ludo Waltman and Nees Jan van Eck
    ## 
    ## Number of nodes: 1920
    ## Number of edges: 63402
    ## 
    ## Running Louvain algorithm...
    ## Maximum modularity in 10 random starts: 0.8022
    ## Number of communities: 7
    ## Elapsed time: 0 seconds

Run non-linear dimensional reduction (UMAP/tSNE)
------------------------------------------------

``` r
cd19 <- RunUMAP(object = cd19, dims = 1:10)
```

    ## Warning: The default method for RunUMAP has changed from calling Python UMAP via reticulate to the R-native UWOT using the cosine metric
    ## To use Python UMAP via reticulate, set umap.method to 'umap-learn' and metric to 'correlation'
    ## This message will be shown once per session

    ## 21:38:37 UMAP embedding parameters a = 0.9922 b = 1.112

    ## 21:38:37 Read 1920 rows and found 10 numeric columns

    ## 21:38:37 Using Annoy for neighbor search, n_neighbors = 30

    ## 21:38:37 Building Annoy index with metric = cosine, n_trees = 50

    ## 0%   10   20   30   40   50   60   70   80   90   100%

    ## [----|----|----|----|----|----|----|----|----|----|

    ## **************************************************|
    ## 21:38:38 Writing NN index file to temp file /var/folders/w7/_7zvpr2x3hqg6h_2tpcsrxl40000gn/T//RtmpDT6tyK/file13032427ff7f
    ## 21:38:38 Searching Annoy index using 1 thread, search_k = 3000
    ## 21:38:38 Annoy recall = 100%
    ## 21:38:38 Commencing smooth kNN distance calibration using 1 thread
    ## 21:38:39 Initializing from normalized Laplacian + noise
    ## 21:38:39 Commencing optimization for 500 epochs, with 73980 positive edges
    ## 21:38:42 Optimization finished

``` r
#```{r fig.height=5, fig.width=7}
dimplot <- DimPlot(object = cd19, reduction = 'umap')
plot(dimplot)
```

![](/images/ID_cells_subsets_v01-04_files/figure-markdown_github/unnamed-chunk-10-1.png)

``` r
#ggplotly(dimplot)

pdf("Output/dimplot.pdf")
plot(dimplot)
dev.off()
```

    ## quartz_off_screen 
    ##                 2

Locate subsets
==============

``` r
#```{r fig.height=5, fig.width=7}
features <- c("CD19", "CD27", "IGHD", "IGHM")
RidgePlot(cd19, features = features, ncol = 2)
```

    ## Picking joint bandwidth of 0.189

    ## Picking joint bandwidth of 0.0882

    ## Picking joint bandwidth of 0.161

    ## Picking joint bandwidth of 0.157

![](/images/ID_cells_subsets_v01-04_files/figure-markdown_github/unnamed-chunk-11-1.png)

Cluster 3 seems to express CD19 CD27 IGHD and IGHM: Marginal Zone (MZ) B cells could be included in the cluster

``` r
vlnplot <- VlnPlot(cd19, features = features, pt.size = 0, ncol=2)
plot(vlnplot)
```

![](/images/ID_cells_subsets_v01-04_files/figure-markdown_github/unnamed-chunk-12-1.png)

``` r
pdf("Output/violin_plot.pdf")
plot(vlnplot)
dev.off()
```

    ## quartz_off_screen 
    ##                 2

This seems to confirm that at least part of the cluster 3 correspond to the MZ pop

``` r
#https://satijalab.org/seurat/v3.1/visualization_vignette.html
DotPlot(cd19, features = features) + RotatedAxis()
```

![](/images/ID_cells_subsets_v01-04_files/figure-markdown_github/unnamed-chunk-13-1.png)

MZ are potentially located in cluster 3 but are a minority of the cluter.

``` r
FeaturePlot <- FeaturePlot(cd19, features = features)
plot(FeaturePlot)
```

![](/images/ID_cells_subsets_v01-04_files/figure-markdown_github/unnamed-chunk-14-1.png)

``` r
pdf("Output/FeaturePlot.pdf")
plot(FeaturePlot)
dev.off()
```

    ## quartz_off_screen 
    ##                 2

Identify markers for all subsets
--------------------------------

[Introduction to Single-cell RNA-seq](https://hbctraining.github.io/scRNA-seq/lessons/09_merged_SC_marker_identification.html) [All lessons](https://github.com/hbctraining/scRNA-seq/tree/master/lessons)

``` r
FeaturePlot(object = cd19, 
                        features = features,
                         sort.cell = TRUE,
                         min.cutoff = 'q10', 
                         label = TRUE)
```

    ## Warning: Using `as.character()` on a quosure is deprecated as of rlang 0.3.0.
    ## Please use `as_label()` or `as_name()` instead.
    ## This warning is displayed once per session.

![](/images/ID_cells_subsets_v01-04_files/figure-markdown_github/unnamed-chunk-15-1.png)

<https://satijalab.org/seurat/v3.1/visualization_vignette.html>

``` r
DoHeatmap(subset(cd19, downsample = 100), features = features, size = 3)
```

![](/images/ID_cells_subsets_v01-04_files/figure-markdown_github/unnamed-chunk-16-1.png)

``` r
#FindMarkers(cd19, ident.1 = 3, ident.2 = c(0,1,2,4:6), min.pct = 0.25)
```

------------------------------------------------------------------------

Manually ID subset
==================

Using the Seurat tutorial allows me to locate cells expressing markers of interest but not to identify cells expressing a combination of markers. Use a manual strategy to obtain the barcode of cells expressing the combination of markers corresponding to MZ cells.

-   <https://stackoverflow.com/questions/29511215/convert-row-names-into-first-column>
-   <https://stackoverflow.com/questions/6778908/transpose-a-data-frame>
-   <https://satijalab.org/seurat/v3.1/pbmc3k_tutorial.html>
-   What does data in a count matrix look like?

``` r
# https://satijalab.org/seurat/v3.1/pbmc3k_tutorial.html
# What does data in a count matrix look like?
# https://github.com/satijalab/seurat/wiki/Seurat#data-access
# https://www.rdocumentation.org/packages/Matrix/versions/1.2-18/topics/dgTMatrix-class
# https://stackoverflow.com/questions/6778908/transpose-a-data-frame

# try to ID cells coexpressing markers working directly with the sparse matrix
features <- c("CD19", "CD27", "IGHD", "IGHM")
cd19_filtered <- cd19.data[features,]

cd19_filtered_df <- dplyr::as_tibble(cd19_filtered)

# cells are collumns feature as row. Need to transpose the data frame with data table

# transpose
t_cd19_filtered_df <- data.table::transpose(cd19_filtered_df)

# get row and colnames in order
#colnames(t_cd19_filtered_df) <- rownames(cd19_filtered_df)
colnames(t_cd19_filtered_df) <- features
rownames(t_cd19_filtered_df) <- colnames(cd19_filtered_df)

#View(t_cd19_filtered_df)

# pb with this approach I don't keep the column name so I need to transform the barcode as a column
# https://stackoverflow.com/questions/29511215/convert-row-names-into-first-column

t_cd19_filtered_df <- tibble::rownames_to_column(t_cd19_filtered_df, "Barcode")
#View(t_cd19_filtered_df)

MZ <- t_cd19_filtered_df %>%
    filter( (CD27 > 0) & (IGHD > 0 & IGHM > 0) )
#View(MZ_2)
nrow(MZ)
```

    ## [1] 75

``` r
#MZ$Barcode

MZ_barcode <- t_cd19_filtered_df %>%
    filter( (CD27 > 0) & (IGHD > 0 & IGHM > 0) ) %>%
    mutate( Barcode2 = paste0(Barcode, "-1")) %>%
    select(Barcode2) 

#View(MZ_barcode)
#write.csv(MZ_barcode, "Output/Extracted_MZ_IGHM/MZ_barcodes.csv")
```

``` r
knitr::kable(head(MZ_barcode))
```

| Barcode2           |
|:-------------------|
| AAACGGGTCACGATGT-1 |
| ACACTGAAGATCGGGT-1 |
| ACACTGAGTTATTCTC-1 |
| ACGAGGATCCCTTGCA-1 |
| ACGCCGACAGGACCCT-1 |
| ACTGTCCTCAGTGCAT-1 |

The barcodes can be used to extract IgH sequences for each subsets and process with clonal analysis.
