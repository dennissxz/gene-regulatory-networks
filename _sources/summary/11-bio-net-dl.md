# Biological networks and deep learning

Muzio, Giulia, Leslie O’Bray, and Karsten Borgwardt. "Biological network analysis with deep learning." Briefings in bioinformatics 22, no. 2 (2021): 1515-1530. [[PDF](https://nbviewer.jupyter.org/github/dennissxz/gene-regulatory-networks/blob/main/reading/bio-net-dl.pdf)] [[DOI](https://doi.org/10.1093/bib/bbaa257)]

Goal
- describe biological networks and review the principles and underlying algorithms of GNNs.
- discuss domains in bioinformatics in which graph neural networks are frequently being applied at the moment, such as
  - protein function prediction,
  - protein–protein interaction prediction,
  - in silico drug discovery and development.
- highlight application areas such as gene regulatory networks and disease diagnosis where deep learning is emerging as a new tool to answer classic questions like gene interaction prediction and automatic disease
prediction from data.

Trend
- experimental high-throughput technologies have expanded the availability and quantity of molecular data in biology
- computing technology

DL result
- The early examples of applying deep learning to biological network data, detailed in this paper, have consistently reported comparable or better results than the existing classical machine learning methods.

## Biological Networks

necessity
- DNA, RNA, proteins, and metabolites have crucial roles in the molecular mechanism of the cellular processes underlying life
- Stying their **structure** and **interaction** is fundamental for
  - development of new druges
  - discovery of disease pathways

It is possible to define biological networks at different levels of detail.

### Protein-Protein Interaction Networks (PPI)

structure
- node: proteins
- edge: interaction among proteins
  - type of edges/interaction: phosphorylation, or bond


Tasks
- node classification: predicting the unknown function of a protein based on the functions of its neighbors in a PPI network.


### Gene Regulatory Networks

Definitions
- gene expression: the set of processes which leads to generating proteins from the DNA sequence

Regulation mechanisms occur at different stages of protein production from DNA (transcription, translation, splicing phases.)

Structure
- node represents a gene
- a directed link among two genes implies that one gene directly regulates the expression of the other without mediation from other genes

Qs: multiple to one? one to multiple? multiple to multiple??

Tasks
- link prediction: which genes regulate the expression of another?

### Metabolic Networks

Definitions
- metabolites: intermediate and final products of metabolic reactions
- metabolic pathways: series of chemical reactions related to perform a specific metabolic function

Structure
- node: metabolite
- directed edge: reaction, labeled with the enzyme acting as the catalyst

### Drug-Drug Interaction Networks (DDI)

Model the interactions among different drugs.

Structure
- node: drug
- edge: interactions

## GNNs

Overview from embeddings to GNN

- recurrent GNNs [84, 85]
  - Gated graph sequence neural networks. ICLR 2016
  - The graph neural network mode. IEEE 2009
- spatial-temporal GNNs [86, 87]
  - Deep leaning on spatio-temporal graphs. CVPR 2016
  - Diffusion convolutional recurrent neural network: Data-driven traffic forecasting. ICLR 2018
- graph embeddings: closely related to GNNs (building blocks), but are not always considered a subset of GNNs.
  - Hamilton W, Ying R, Leskovec J. Representation learning on graphs: Methods and applications. IEEE Data Eng Bull, 2017.
  - Cui P, Wang X, Pei J, et al. A survey on network embedding. IEEE Trans Knowl Data Eng 2019;31:833–52.
  - Cai H, Zheng VW, Chang KC. A comprehensive survey of graph embedding: Problems, techniques, and applications. IEEE Trans Knowl Data Eng 2018;30(9):1616–37.
- graph GCNs: building blocks of many other GNN architectures




Development
- Spectral methods, first introduced by Bruna et al. [93] and later Defferrard et al. [94], build a convolution by creating a **spectral filter** defined in the Fourier domain using the graph Laplacian.
  - However, due to the computational complexity of the eigendecomposition of the graph Laplacian necessary for spectral methods, many more methods have been developed using **spatial methods**, where the idea is to learn an embedding for each node by aggregating its neighborhood in each successive layer in the network
- Spatial methods
  - Duvenaud [95], providing a permutation-invariant convolution that operates over all nodes in the graph
  - Kipf and Welling GCNs: bridge the gap between spatial and spectral methods by showing a spectral motivation for their spatial approach.
    - Though this approach was originally proposed as a way to perform node classification via semi-supervised learning, it can be easily generalized to classify higher-order structures in the graph, edge level outcomes, or the graph itself.
  - Hamilton+ GraphSAGE: generalizable and computationally efficient approach
    - sampling neighbors
    - learning an aggregation function
- Niepert et al. [30] solve the node correspondence problem by imposing an ordering upon the graph, and in doing so opens the door to utilize a more traditional CNN structure.


Relation to
- message passing: Gilmer et al. [36]
- WL kernel: The adaptations in GCNs can therefore be seen as a differentiable and continuous extension of the Weisfeiler–Lehman algorithm and kernel

## Application in Biology

### Proteomics

#### Protein-Protein Interaction Prediction

Traditionally, many methods use the primary structure of amino acid sequences in order to vectorize a protein and perform classification. However, the recent methods that leverage the graph structure have shown stronger performance compared to merely using the sequence information.

- Yue [41]: network embedding + DL
- Liu [60]: GCN
- DeepPPI[59]: used deep learning on a vector summary of the protein sequences to predict links. outperform classical methods.

### Protein Function Prediction

Motivation
- manual assessment of the large amounts of data resulting from high-throughput experiments is rather slow and costly

Can be formulated as node classification task or a graph classification task.

Gligorijevic et al. [71] consider the idea of representing PPI networks using **multiple** representations of the same network. Each network contains different information, but uses the same set of nodes. They create a vector representation of each node using Random Walk with Restarts from Cao et al. [101] to then construct a positive point-wise mutual information matrix for each of the adjacency matrices, which is used as the input to a multimodal deep autoencoder.

Zeng et al. [56] seek to identify essential proteins from a PPI network. They learn a dense vector representation of each node using node2vec [54], and combine that with a representation learned from gene expression profiles using a RNN (!!). This is then passed through a regular fully connected network, in order to classify each node as an essential or non-essential protein.

### Gene Regulatory Network

Dataset limitation
- curated GRN datasets are not yet available or are difficult to obtain for a large number of organisms [49, 121]
- gene expression data are intrinsically noisy [122] and therefore not ideal for training models.


For this reason, GRNs are mostly analyzed with unsupervised methods [121], since supervised techniques, and deep learning in particular, require a large number of well annotated samples in order to be effective.

However, some deep learning models, specifically RNNs, report promising results, although they do not use any kind of graph information to perform the task. One example is the work in [122], which enhances the training quality by introducing a non-linear Kalman filter, which deals very effectively with the noise in the data

Turki et al. [49] present an example of graph-based deep learning approaches. The authors use an unsupervised method to obtain a preliminary version of the GRN from gene expression time series data, which is **denoised** through a cleaning algorithm, and then used to train diverse supervised methods to perform link prediction among gene pairs.
- The proposed data cleaning algorithm is of crucial importance and could positively impact the field of GRN analyses since it increases the quality of the GRN data. More in detail, the denoised features are obtained by projecting the original features onto the eigenvectors of the distance matrix of the feature vectors calculated using the Laplacian kernel function. The supervised methods Turki et al. use after cleaning the GRN includes SVMs and deep learning approaches, such as a DNN and a deep belief network. The latter two outperform the unsupervised state-of-the-art baseline, although failed to outperform the linear SVM-based approach.


## Reference of interest

- 49: Turki T,Wang JTL, Rajikhan I. Inferring gene regulatory networks by combining supervised and unsupervised methods. In: Proceedings of the 15th IEEE International Conference on Machine Learning and Applications (ICMLA), 2016, pp. 140–5
- 121: Maetschke SR, Madhamshettiwar PB, Davis MJ, et al. Supervised, semi-supervised and unsupervised inference of gene regulatory networks. Bioinformatics 2013;15(2):192–211.
- 122: Raza K, Alam M. Recurrent neural network based hybrid model for reconstructing gene regulatory network. Comput Biol Chem 2016;64:322–34.

Takeaway for writing
- graph notation
- graph embedding
- development of GNNs
