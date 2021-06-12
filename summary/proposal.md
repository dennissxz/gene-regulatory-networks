
# Inference of Gene Regulatory Networks by Graphical Neural Networks


## Goals and Motivation

A gene that plays a role in regulating gene expression at transcription stage is called a transcription factor (TF), and the genes that are controlled by it are called gene targets. Studying gene regulations is essential for elucidating gene functions, interpreting biological processes, and prioritizing candidate genes for molecular regulators and biomarkers in complex diseases and traits analyses. Inferring gene regulatory relationships between a set of TFs and a set of potential gene targets through gene expression data is still far from being resolved in bioinformatics.

## Problem Formulation

TFs and targets can be represented by a bipartite graph $G = (V_{TF}, V_{target}, E)$, where $V_{TF}$ is the vertex set of TFs and $V_{target}$ is the vertex set of targets. If there is an regulatory relation (activatory or inhibitory) between a TF $v_{TF}$ and a target $v_{target}$, then there is a directed edge $e(v_{TF}, v_{target})$ between them. Such graphical representation of the gene regulatory relationships is called a gene regulatory network (GRN). Note that it is possible to have an edge between two TFs, and self-loops.

For each gene $i$, the vertex attribute vector $\boldsymbol{x}_i \in \mathbb{R} ^m$ typically consists of RNA relative expression levels measured for that gene over a compendium of $m$ experiments, e.g. changes in pH, growing media, heat, oxygen concentrations, and genetic composition. The attribute vector can be time-series as well.

Given a set of TFs $V_{TF}$, a set of targets $V_{target}$ and their attribute vectors $\boldsymbol{x} _i$ for $i \in V$, we want to infer the edge set $E$, which is a link prediction problem in a gene regulatory network.

- given E, how to generate X probablistically?

## Related Literature

Many machine learning methods are applied to solve the link prediction problem in GRN. For simplicity, most methods only consider undirected edges.

Unsupervised methods dominated GRN inference decades ago due to limited amount of known TF/target pairs. These methods include regression-based methods and information/correlation-based method. For the latter one, we usually construct a weight $w(\boldsymbol{x} _{TF}, \boldsymbol{x} _{target})$ between a TF/target pair using their attribute vectors, such as mutual information or Pearson's correlation coefficient. If this weight is larger than some threshold, then we say that an edge exists between these two genes.

In recent years, due to the identification of a large number of TFs and their targets, supervised methods have been developed. For a known regulatory pair of TF/target pairs and their attribute vectors, we construct a feature vector $\boldsymbol{x} = \operatorname{agg}(\boldsymbol{x} _{TF}, \boldsymbol{x} _{target})$ by aggregating the two attribute vectors, e.g. concatenation, and assign a positive label. For other TF/target pairs where there are no regulatory relations, we assign a negative label. The processed data is then used for supervised training.

Maetschke [1] compares supervised and unsupervised methods and conclude that even a supervised method with limited amount of labeled data outperforms unsupervised methods. Besides, Turki et al. [2] developed a de-noise algorithm for gene expression data and show improved performance. And depending on the data type of gene attribute vector $\boldsymbol{x}$, time-series models are also developed [7].

With recent advancements in deep learning, in particular, efforts are made to solve the link prediction problem in gene regulatory networks by garphical neural networks (GNNs), whish is particularly designed for graph-type data and problems. For example, Wang et al. [6] developed gene-regulatory graphical neural networks (GRGNN) that uses an ensemble method to aggreagte noisy information from unsupervised methods.

## Research Questions

Recent graphical neural networks models for GRN take non-time-series gene expression data as input. There are GNN models designed for time-seires data [8] for forecasting purpose. Can we modify these methods to time-series type gene expression data for link prediction purpose? Will they perform better than other machine learning algorithm for time-seires gene expression data?


## Data and codebases

Curated GRN datasets are not yet available or are difficult to obtain for a large number of organisms and gene expression data are intrinsically noisy and therefore not ideal for training models [3]. Some commonly used datasets are

- DREAM4: Datasets of gene expression time series data & associated ground truth GRN structure from the DREAM4 100-gene in silico network inference challenge [4].

- DREAM5: Datasets of gene expression (time series and independent observations) [5].

The codebase of gene-regulatory graphical neural networks (GRGNN) [6] is also available on Github.


## References

[1] [[PDF (main)](https://nbviewer.jupyter.org/github/dennissxz/gene-regulatory-networks/blob/main/reading/grn-ssu.pdf)] [[PDF (with supplementary material)](https://arxiv.org/pdf/1301.1083.pdf)] Maetschke, Stefan R., Piyush B. Madhamshettiwar, Melissa J. Davis, and Mark A. Ragan. "Supervised, semi-supervised and unsupervised inference of gene regulatory networks." Briefings in bioinformatics 15, no. 2 (2014): 195-211.

[2] [[PDF](https://web.njit.edu/~wangj/publications/ARTICLES/ICMLA2016.pdf)] Turki, Turki, Jason TL Wang, and Ibrahim Rajikhan. "Inferring gene regulatory networks by combining supervised and unsupervised methods." In 2016 15th IEEE International Conference on Machine Learning and Applications (ICMLA), pp. 140-145. IEEE, 2016.

[3] [[PDF](https://nbviewer.jupyter.org/github/dennissxz/gene-regulatory-networks/blob/main/reading/bio-net-dl.pdf)] [[DOI](https://doi.org/10.1093/bib/bbaa257)] Muzio, Giulia, Leslie O’Bray, and Karsten Borgwardt. "Biological network analysis with deep learning." Briefings in bioinformatics 22, no. 2 (2021): 1515-1530.

[4] Greenfield A, Madar A, Ostrer H, Bonneau R: DREAM4: combining genetic and dynamic information to identify biological networks and dynamical models. PloS one 2010, 5(10):e13397.

[5] [[DOI](https://www.nature.com/articles/nmeth.2016)] Marbach D, Costello J, Ku«ffner R, et al. Wisdom of crowds for robust gene network inference. Nat Methods 2012;9(8): 796–804.

[6] [[PDF](https://nbviewer.jupyter.org/github/dennissxz/gene-regulatory-networks/blob/main/reading/grn-grgnn.pdf)] [[DOI](https://www.sciencedirect.com/science/article/pii/S200103702030444X?via%3Dihub)] [[Github](https://github.com/juexinwang/GRGNN)] Wang, Juexin, Anjun Ma, Qin Ma, Dong Xu, and Trupti Joshi. "Inductive inference of gene regulatory network using supervised and semi-supervised graph neural networks." Computational and Structural Biotechnology Journal 18 (2020): 3335-3343.

[7] [[PDF](https://bmcbioinformatics.biomedcentral.com/track/pdf/10.1186/s12859-018-2125-2.pdf)] Thorne, Thomas. "Approximate inference of gene regulatory network models from RNA-Seq time series data." BMC bioinformatics 19, no. 1 (2018): 1-12.

[8] [[PDF](https://papers.nips.cc/paper/2020/file/cdf6581cb7aca4b7e19ef136c6e601a5-Paper.pdf)] Cao, Defu, Yujing Wang, Juanyong Duan, Ce Zhang, Xia Zhu, Conguri Huang, Yunhai Tong et al. "Spectral temporal graph neural network for multivariate time-series forecasting." arXiv preprint arXiv:2103.07719 (2021).
