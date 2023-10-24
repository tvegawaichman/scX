<h1 align="center">  scX package </h1>

<div align="justify" >
<img align="left" width="25%" src="man/figures/scXplorer-03.png" title="@irimiodo"> scX is an R package that enables interactive visualization and analysis of single-cell experiment data by creating a Shiny-based application. With scX all aspects of single-cell data can be explored using a large number of different types of plots, such as scatter plots, heatmaps, boxplots, dot and violins plots, etc. All the information associated with cells can be displayed in a customized way: both numerical variables such as logcounts or pseudotime values, and categorical variables such as cell types or sample. One of the main hallmarks of scX is the possibility to plot the main embeddings used for single cell - UMAP, tSNE and PCA - both in 2D and 3D in an interactive way. Thus, embeddings can be rotated, translated and zoomed. But scX is not only a visualization tool, it also allows you to perform different types of analysis on single cell data, such as finding the markers of a cell type or determining the differential genes between two different conditions.
</div>

---

<table width="100%"  style="border:0px solid white; width:100%;" >
<tr style="border: 0px;" >
<td width="33%" style="border:0px; width:33.33%" >
<h2 align="left">  Table of Contents </h2>

- [Install](#install)
- [Quick Start Guide](#quick-start-guide)
  * [Summary](#summary)
  * [Markers](#markers)
  * [Gene expression](#gene-expression)
  * [Differential expression](#differential-expression)
  * <nobr> [Exploratory Data Analysis](#exploratory-data-analysis) </nobr>
  * [Visual Tools](#visual-tools)
- [FAQs](#faqs)
</td>

<td width="66.66%" style="border:0px; width:66.66%">
 
<img src="man/figures/presentation_t2.gif" width="100%" />

</td>
</tr>
</table>

## Install

scX requires certain packages to be installed. If the user does not have these packages installed, they can execute the following code: 

```R
BiocManager::install(c("SingleCellExperiment","scran","scatter","ComplexHeatmap")
```

scX can be installed from Github as follows:

```R
devtools::install_github("tvegawaichman/scX")
```
## Quick Start Guide
### Loading Datasets

To show the different capabilities of scX, we will use single cell data related to the oligodendrocyte developmental lineage ([Marques et al. 2016](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5221728/)). In this dataset we have cells from 12 regions of the central nervous system of juvenile and adult mice and 10 distinct cell populations have been identified. This package includes a modified version of this dataset in which the original cells have been subsampled and a pseudotime has been calculated to demonstrate how scX can represent numerical attributes.

In this example we will use the 'inferred_cell_type' covariate to explore differential expression patterns (partitionVars parameter). The rest of the metadata will be used for visualization purposes (metadataVars parameter). While partitionVars should be categorical variables, metadataVars can be discrete or continuous covariates

```R
library(scX)

#SCE example data
sce

colData(sce)[1:3,]
# DataFrame with 3 rows and 8 columns
#                                   title     source_name         age inferred_cell_type                    sex      strain   treatment pseudotime
#                             <character>     <character> <character>        <character>            <character> <character> <character>  <numeric>
#   C1-1771017-030-C09 C1-1771017-030-C09       cortex S1         p22                OPC pooled male and female  PDGFRa-GFP          No    23.1662
#   C1-1771017-028-G05 C1-1771017-028-G05 hippocampus CA1         p22                OPC                      F  PDGFRa-GFP          No    21.7566
#   C1-1771052-132-B02 C1-1771052-132-B02 corpus callosum         p69                OPC                      M         CD1          No    23.3207
```

<p align="justify">  
The scX app can be created and launched with only two functions. **_createSCEobject()_** creates the single cell experiment object that will be used within the application. This function performs a set of preprocessing steps required for the various scX functions. Among these steps we can mention:
 
1. Calculation of quality control metrics. 

2. If no partition was declared in the "partitionVars" parameter, a preliminary clustering will be calculated with the scran package function _quickCluster()_.

3. Normalization of the gene expression matrix.

4. Determination of the most variable genes (HVG).

5. Calculation of different dimensionality reductions: PCA, tSNE, and UMAP.

If the input dataset already has these characteristics, some of these steps can be avoided.  For example, if the dataset already has a precomputed PCA, scX will not recalculate it but will calculate the dimensionality reductions that are not already calculated in the dataset.

On the other hand, different options for the calculation of the marker genes of the different clusters can be determined. This can be done within the parameter "paramFindMarkers" which expects a list of parameters taken by scran's _FindMarkers()_ function. 

Finally with the **_launch_scX()_** function the application is deployed.

</p>

```R
library(scX)
# Creating SCE object
cseo <- createSCEobject(xx = sce, 
                        partitionVars = "inferred_cell_type", 
                        metadataVars = c("source_name", "age", "sex", "strain", "treatment", "pseudotime"),
                        descriptionText = "Quick Start Guide")

launch_scX(cseo)
```
## Summary

<p align="justify">  
scX displays a summary of the main descriptive information of the dataset: number of cells and genes, mean number of genes detected per cell, partions, experiments and average library size.

In the summary section, you can explore through graphical visualization the coventional quality control metrics: this is the relationship between the number of features and the count numbers. In this plot you can distinguish the cells coming from the different partitions of the dataset.
</p>

<details><summary> <h5> View more </h5> </summary>

 <img src="man/figures/summary.gif" width="100%" />
 
</details>

##  Markers

<p align="justify">  
This section allows to find and interact with the markers of each different clusterization. In "Markers" section there are two types of analysis. On the one hand, in "Cluster markers" clicking on a cell displays a table with the marker genes of the cluster to which that cell belongs. On the other hand, in "Find new markers" you can select a group of cells in the embedding and scX will calculate their marker genes.
</p>

<details><summary> <h5> View more </h5> </summary>
 
###  Clusters markers 

<p align="justify">  
This section allows you to find the marker genes for the partition defined in the single-cell object, typically cell types or cell states. Clicking on one of the cells in the embedding will display a table of marker genes for the partition to which that cell belongs. For each of the markers different metrics such as boxcor, robustness and FDR are displayed. This table can be downloaded in various formats, such as .csv, .xlsx .pdf, or you can copy it to the clipboard. 

By clicking on a marker in the table you can see its expression profile across the entire dataset in the embedding. In addition violin and spikeplots are displayed at the bottom.
</p>


<img src="man/figures/cluster_markers.gif" width="100%" />


### Find new markers 

<p align="justify">  
Here you can select with the box or lasso tool a set of cells in the embedding and scX will calculate the marker genes. You can download not only the marker table but also the selected cell list.

As in the previous section, if you click on one of the markers you can see its expression along the dataset with violin and spikeplots.
</p>


<img src="man/figures/new_markers.gif" width="100%" />
</details>


##  Gene Expression

<p align="justify">  
In "Gene Expression" you can explore different aspects of the expression of one or more genes of interest. Determine how expression changes according to different categorical and continuous variables, as well as analyse co-detection between pairs of genes.
</p>

<details><summary> <h5> View more </h5> </summary>
 
### Categories

<p align="justify"> 
In Settings you can select one or more genes or upload a file with a list of genes. The average expression of the genes of interest can be viewed in the different embeddings available, with the possibility to colour according to the different SCE partitions to compare gene expression with different cell types or conditions present in the metadata. 

A wide variety of plots are available to analyse the expression of the genes of interest in the different categories. Heatmaps allow normalisation of expression by gene, clustering by row and column and grouping of cells by condition. Similarly, dotplots allow normalisation of expression and group genes with similar expression pattern.
</p>


<img src="man/figures/categories.gif" width="100%" />


### Fields

<p align="justify"> 
Fields allows you to analyse the expression of a set of genes in relation to numeric variables present in your dataset, such as the number of counts or pseudotime value, if present in the metadata of the sce object. Below the embedding, a line plot of the average expression of the genes of interest as a function of the chosen variables and a spikeplot are displayed. Furthermore, you can find heatmaps sorted by the chosen numerical variable that can be divided according to some categorical variable, and multiline plots showing the comparison of the expression profile of the genes of interest along the field.
</p>


<img src="man/figures/ge_fields.gif" width="100%" />



### Co-expression

<p align="justify"> 
In Co-expression section you can analyse the co-appearance of pairs of genes, determine the number and percentage of cells in which each gene is expressed separately and together. You can also view this information graphically in the embedding. In addition, the co-expression of these genes in the different conditions of any of the partitions in the dataset can be analysed by a co-detection matrix.
</p>


<img src="man/figures/coexpression.gif" width="100%" />

</details>

## Differential expression
<p align="justify"> 
In this section, the differentially expressed genes between two clusters can be obtained. The algorithm used is `findMarkers()` from the scran package and the test type can be defined in the `createSCEobject()` function (see "Summary" section). Different significance levels can be chosen interactively for both the logFC and the FDR. Once the clusters have been chosen, a table will be displayed with the list of differentially expressed genes accompanied by the logFC and the FDR obtained for each of them. This table can be downloaded in different formats: csv, pdf, xlsx. 

The main figure in this section is a VolcanoPlot in which genes that are down and up expressed are coloured in blue and red, respectively. On the other hand, you can also display ViolinPlots, Spikeplots, Heatmaps and Dotplots of the differentially expressed genes.
</p>

<details><summary> <h5> View more </h5> </summary>

 <img src="man/figures/differential_expression.gif" width="100%" />

</details>

##  Exploratory Data Analysis

In **Exploratory Data Analysis** section you will be able to understand the relationship between different features contained as metadata in your SCE object.

<details><summary> <h5> View more </h5> </summary>
 
### Categories

<p align="justify"> 
Here you can observe the proportion of cells belonging to the different levels of a categorical variable presented in the metadata and disaggregate these proportions according to the levels of another categorical variable. All this information is displayed in the form of a barplot. In the subsection "Matrix" a confusion matrix between the two selected features can be plotted with the option to display the Jaccard index for each of the grid cells. In addition, the Rand index is displayed, which is a global measure of the similarity between the two clusterings.
</p>

<img src="man/figures/exploratory_categories.gif" width="100%" />



### Fields

In a similar way to the previous subsection, in "Field" you can explore how the value of one or more numerical variables changes as a function of another variable, either numerical or categorical. You can make different types of plots such as: Distribution Plots, Heatmaps, Dotplots and StackedViolins.

<img src="man/figures/fields.gif" width="100%" />


</details>

##  Visual Tools

In the "Visual Tools" section, different plots can be obtained to explore in more depth different aspects of the single-cell experiment data, such as how the expression of a given set of genes varies at different levels of a feature or to recognise a set of cells of interest within an embedding.

<details><summary> <h5> View more </h5> </summary>

### Violin by Partition 

By selecting or uploading a set of genes of interest, a set of Violin plots can be obtained and download in .pdf format, allowing to facet the plots by differents categorical co-variables.

<img src="man/figures/violins.gif" width="100%" />

###  Multiplots

 Multiplots allows you to explore how different variables change across cells in an embedding of your choice, such as the expression of a given set of genes, the partitions of a categorical variable or the value of a continuous variable.
 
<img src="man/figures/multiplots.gif" width="100%" />


</details>

## FAQs

<details><summary> <h3> 1 -   What type of input does scX accept? </h3> </summary><blockquote>

</blockquote></details>

<details><summary> <h3> 2 -  Is there a maximum input size that scX can receive? </h3> </summary><blockquote>

</blockquote></details>

<details><summary> <h3> 3 -  How can I deploy my scX app online? </h3> </summary><blockquote>

</blockquote></details>

