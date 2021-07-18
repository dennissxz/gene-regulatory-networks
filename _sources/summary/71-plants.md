# For plants

[[PDF](https://nbviewer.jupyter.org/github/dennissxz/gene-regulatory-networks/blob/main/reading/grn-plant.pdf)][[DOI](https://www.frontiersin.org/articles/10.3389/fpls.2018.01770/full)] Mochida, Keiichi, Satoru Koda, Komaki Inoue, and Ryuei Nishii. "Statistical and machine learning approaches to predict gene regulatory networks from transcriptome datasets." Frontiers in plant science 9 (2018): 1770.


This review summarizes the recent advancements in the computational inference of GRNs, based on large-scale transcriptome sequencing datasets of model **plants** and crops. We highlight strategies to select contextual genes for GRN inference, and statistical and ML-based methods for inferring GRNs based on transcriptome datasets from plants. Furthermore, we discuss the challenges and opportunities for the elucidation of GRNs based on large-scale datasets obtained from emerging transcriptomic applications, based on population-scale, single-cell level, or lifecourse analyses.


Related work

- Constructing an initial network by assumption-free methods, such as information theory-based methods or co-expression analysis, would be feasible to minimize false-positive edges with high computational efficiency in GRN inference, enabling us to apply statistical or ML-based methods to examine causalities between genes with respect to each local subnetwork in the initial networks (Liu et al., 2016).

- To predict the drought stress-responsive GRN in sunflower, Marchand et al. (2014) selected 145 genes that were co-expressed under drought stress conditions, and subsequently used a Gaussian graphical modeling method and a Random Forest method to infer the robust edges (Marchand et al., 2014).

## Statistic based

Since time-series datasets contain dynamic information, which assists us in understanding temporal dynamics of various biological processes, statistics-based approaches have been often applied to time-series transcriptome datasets to infer GRNs.

### Time-series

- Autoregressive exogenous variable (ARX) model
  - a kind of state-space model used to describe time-varying processes observed in time-series datasets
  - can be used in combination with sparse estimation algorithms
- Fused Lasso
  - a sparse estiamtion algorithm
  - used to reconstruct GRNs with time-series expression datasets (Omranian 2016)
- Group SCAD (Koda 2017)
- Inferelator (Greenfield 2013)
- GENIST (de Luis Balaguer 2017)
  - based on a dynamic Bayesian network
  - outperformed with datasets used in DREAM 4 challenge 2.


### Non-time series

- Context Likelihood of Relatedness (Faith 2007)
  - a mutual information based approach
- Partial response coefficients (Blum 2018)


## Machine Learning-Based

- MinReg (Guo 2016)
  - data: Various biological and experimental conditions of Fusarium graminearum
- GENIE3 (Huynh-Thu 2010)
  - a tree-based algorithm
- Dynamic factor graph (DFG models)

## Dataset





## ML based
