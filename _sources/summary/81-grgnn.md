# GRGNN

[[PDF](https://nbviewer.jupyter.org/github/dennissxz/gene-regulatory-networks/blob/main/reading/grn-grgnn.pdf)] [[DOI](https://www.sciencedirect.com/science/article/pii/S200103702030444X?via%3Dihub)] [[Github](https://github.com/juexinwang/GRGNN)] Wang, Juexin, Anjun Ma, Qin Ma, Dong Xu, and Trupti Joshi. "Inductive inference of gene regulatory network using supervised and semi-supervised graph neural networks." Computational and Structural Biotechnology Journal 18 (2020): 3335-3343.

We propose an end-to-end gene regulatory graph neural network (GRGNN) approach to reconstruct GRNs from scratch utilizing the gene expression data, in both a supervised and a semisupervised framework.

highlight: !! link prediction to graph classification

- To get better inductive generalization capability, GRN inference is formulated as a graph classification problem, to distinguish whether a **subgraph** **centered** at two nodes contains the link between the two nodes
- A linked pair between a transcription factor (TF) and a target gene, and their neighbors are labeled as a positive subgraph, while an unlinked TF and target gene pair and their neighbors are labeled as a negative subgraph.

Model intro
- A GNN model is constructed with node features from both explicit gene expression and graph embedding.
- We demonstrate a noisy starting graph structure built from partial information (i.e. noisy skeleton) based on heuristics on gene expression data, such as Pearson’s correlation coefficient and mutual information which can help guide the GRN inference through an appropriate **ensemble** technique.

To the best of our knowledge, this is the first work to infer gene regulatory networks through graph neural networks. Our contributions in this paper are
1. introduction of a supervised/semisupervised graph classification framework for gene regulatory network inference,
2. using noisy starting skeleton to guide link prediction in the graph,
3. efforts of **inductive** inference GRN across unknown species and conditions (?? GRNs can be constructed with the same input as the unsupervised methods without using new labels in the new condition or species)

Dataset: DREAM5 GRN inference benchmarks.
- Take E. coli for example, there are 334 TF and 4177 Targets, in total 344*(344+4177-1)=1,506,340 possible links in total, and only 2,066 among them are confirmed gold standard positive links

## Review Methods and Limitation

Unsupervised Methods
- regression-based (TFs are selected by target gene through sparse linear-regression)
  - TIGRESS [14]
- information-based methods (ranking edges based on variants of mutual informaiton)
  - ARACNE
  - CLR
  - MRNET
- correlation-basd methods
- Bayesian networks by optimizing posterior prob using different heuristic searches [1]
- GENIE3 [15] is a well-established and widely accepted method based on ensemble random forest regression of gene expression levels between TF and targets. Obtained best performance among all the methods at that time.

Supervised methods and limitations
- Because of heterozygous data sources, these supervised methods usually have limited generalizable capabilities in complex biological mechanisms.
- For a practical GRN inferring problem, there is usually no known relationship ready for training, which makes it unfeasible to predict gene regulatory relationships inductively in practice.

Motivation: Motif
- Gene regulatory activities always act as a whole system with a set of genes to perform a biological function [19].
- Network motif [2] is a widely accepted biological hypothesis,
- Existing supervised GRN inferring methods usually only take the two endpoints of the regulatory interactions as the input, and then treat these known TF/target gene interactions independently in the training processes, and hence neglect the **global** relationships among these interactions.

## Model

Hypothesis
- the features of two nodes and their neighbors (local structure) can decide whether they form a TF and target gene pair, which is consistent with the network motif hypothesis.

### Construct noisy skeletons

$$
A_{t, g^{\prime}}=\left\{\begin{array}{ccc}
1 & \text { if } & H_{i}(t, g)>=\text { threshold }_{i} \\
0 & & \text { otherwise }
\end{array}\right.
$$

- use heuristic methods $H(t, g)$, e.g. correlation, MI
- threshold are set as the parameters for training


These inferred links are noisy, but integrating these links as a starting skeleton can guide training.

### Extract enclosed subgraphs

For each known $(t, g )\in E$
- extract positive subgrah $SG_i(t, g) ^+$ from noisy skeleton $i$
  - include $h$-hop neighbors on noisy skeleton
  - choice of $h$ depend on complexity of data


Randomly select $(t, g )\notin E$
- extract negative subgraph $SG(t, g) ^-$ from noisy skeleton $i$
  - include $h$-hop neighbors on noisy skeleton
- may select false negatives due to undiscovered regulatory relationship, but common practice for choosing negative examples

To get a balanced dataset, set same size. A semi-supervised learning strategy is introduced to select a Reliable Negative sample set from the unlabeled datasets. See 2.5.

### Add node labels and features

A perfect hashing labelling [39] function $label(i)$ is used for marking node $i$’ s roles in $SG(t, g)$

$$
\text { label }(i)=\left\{\begin{array}{cc}
1 & \text { if } \quad i=t \mid g \\
1+\min \left(d_{t}+d_{g}+(d / 2)[(d / 2)+(d \% 2)-1]\right) & \text { otherwise }
\end{array}\right.
$$

where
- $d_i = d(i, t)$
- $d_g = d(i, g)$
- $d = d_t + d_g$
- $(d/2)$ and $d \%2$ are the integer quotient and remainder of $d$ divided by $2$.
- min (??)

why add node labels??

Only the centered target nodes $t$ and $g$ are labeled with 1, while the importance of nodes decreases when the node is far away from the center nodes. With appropriate labels, GNN can learn the structural information whether a link exists between the target nodes.

Node features include
- **explicit features** computed from gene expression data
  - use $z$-score [6] and summary statistics: sd, four quantile percentages
- **structural embeddings**, e.g node2vec, from this noisy skeleton $GRN_i ^\prime$.

### Build ensemble GNN classifiers

DGCNN [40] is used for graph classification, which adopts a quasi Weisfeiler-Lehman subtree model [34] to extract nodes’ local substructure features, and pool these nodes in order.

Finally, a convolutional network (CNN) follows to read sorted (node label used here??) graph representations and make predictions.

Ensemble method combines prediction from each GNN that is built for each heuristic function. The weights can be trained either through a neural network or a simple least squre regression. This paper demonstrates a $k=2$ case and simply manually set equal weights $(0.5, 0.5)$.

## Experiments

Use E. coli and S. cerevisiae in DREAM5 challenge.

### Supervised + Transductive

- Task: predict unknown given parts of the known in the same species.
- Evaluation: 3-fold cross-validation.
- baseline: SVM, RF
- $h=1$, explicit feature dim=1.

Ensembled GRGNN performs better or at least the same with SVM/RF in GRN inferences in nearly all the criteria.

### Supervised + Inductive

- Task: The model trained from E. coli was applied to predict regulatory relationships in S. cerevisiae, and the model trained from S. cerevisiae was used to predict E. coli.



Findings:
- Ensembled GRGNN outperforms SVM/RF in GRN inferences in nearly all the criteria.
- 1-hop GRGNN outperforms 0-hop GRGNN persistently in most of the criteria of both datasets, , which indicates integrating neighbors brings more predictive power to the graph model.
- adding graph embedding of the enclosed subgraph generally improves the performances for GRGNN.

### Unsupervised

GRGNN inductive compared with unsupervised method.

GRGNN outperforms all other methods on both ROC curve and Precision-Recall curve.

### Number of Layers

How to choose $d$ in $d$-hop GRGNN? Experiment $h=0$ to 9.

Findings:
- adding 1- hop to 0-hop brings extra predictive power with the neighbors as the local structure in the graph. After that, adding more hops does
not seem to bring significantly better results in GRN.
- This phenomenon may indicate that few hops of GNN contain almost all information for link prediction from its local structure in the graph,

### Heuristic Functions

- compared with random skeleton, better performance (3.6)
- robust to threshold. (3.7)


## Takeaway

### Limitation

The main limitation of this work is the datasets used. E. coli and S. cerevisiae are relatively well-studied small model species. These data are the **only benchmark** having systematically clear, experimental validated, gold standard regulatory relationships.

### Ref

[39] SEAL: Zhang Muhan, Chen Yixin. Link prediction based on graph neural networks. Advances in Neural Information Processing Systems 31. 2018. p. 5165–75. http://papers.nips.cc/paper/7763-linkprediction-based-on-graph-neural-networks.pdf.

[40] DGCNN: Zhang Muhan, Cui Zhicheng, Neumann Marion, Chen Yixin. An end-to-end deep learning architecture for graph classification. Thirty-Second AAAI Conference on Artificial Intelligence, 2018. https://www.aaai.org/ocs/index.php/AAAI/AAAI18/paper/view/17146.

[15] GENIE: Huynh-Thu V, Irrthum A, Wehenkel L, et al. Inferring regulatory networks from expression data using tree-based methods. PLoS One 2010;5(9):e12776.

[6] z-score

### Qs

- How to 'center' two nodes in a graph in GNN for graph classification?
- z-score $R_i$?

### For writing

introduction
- necessity of GRN.
- classical methods
