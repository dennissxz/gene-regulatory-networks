# A Denoise Algorithm

[[PDF](https://web.njit.edu/~wangj/publications/ARTICLES/ICMLA2016.pdf)] Turki, Turki, Jason TL Wang, and Ibrahim Rajikhan. "Inferring gene regulatory networks by combining supervised and unsupervised methods." In 2016 15th IEEE International Conference on Machine Learning and Applications (ICMLA), pp. 140-145. IEEE, 2016.

One-line summary
- they develop a denoise algorithm on gene expression data before fitting the data to supervised algorithm that improves the classification performance.

## Abstract

Motivation

- Supervised methods for inferring gene regulatory networks (GRNs) perform well with good training data. However, when training data is absent, these methods are not applicable.
- Unsupervised methods do not need training data but their accuracy is low.
- In this paper, we combine them.

Hightlight

- We use results obtained from unsupervised methods to train supervised methods.
- Since the results contain noise, we develop a **data cleaning algorithm** to remove noise, hence improving the quality of the training data.
- These refined training data are then used to guide classifiers, e.g. SVM, NN for link prediction



## Related Work

### Unsupservised Methods

BANJO [11]: Bayesian Network Inference with Jave Objects
- models GRNs as a first-order Markov process
- searches through all possible GRNs for the one with the best score


TimeDelay-ARACNE [12]
- use mutual information

tlCLR [13, 14]
- use mutual information and ODE

DFG [15]: Dynamic Factor Graphs
- model experimental noise as a fitted noise

Jump3 [16]
- noon-parametric, based on decision trees

ScanBMA [17]
- incorporates external information

Inferelator [18]
- uses ODE
- outputs a list of **ordered** gene pairs where each gene pair is associated with a positive, non-zero right $W(u, v) \in (0, 1]$. (score??)
- gene pairs not shown in the output list are assumed to have a weight of $-1$ (why not shown??)


### Supervised Methods

performance depends on the quality and the amount of available training data.

SVM [8,9,19,20].

## Method

Overview
- use unsupervised method to infer a GRN, where links contains noise
- sample positive amd negative links from this GRN, denoise and obtain cleaned data set
- training supervised method based on the cleaned data set

### Feature Vector Construction

Given gene expression data, use an unsup method (Inferelator in this paper) to infer a GRN, which is a weighted directed graph $G=(V, E, W)$
  - node: gene
  - **directed** link $e$: inferred regulatory relationship
  - weight: $W(e) \in (0,1]$

Traditional methods sample $E_{sample}$ (both positive and negative) from $E$. And then use the gene expression data for gene nodes in the induced graph of set $E_{sample}$, to train supervised methods. But unsup methods often creates missing and spurious links in $E$
  - missing link: $e_m \notin G$ but in ground truth GRN
  - spurious link: $e_s \in G$ but not in ground truth GRN

To improve this, we add a denoise step.

### Data Clearning Algorithm

Input

- In $E_{sample}$, we have $m$ links with the largest weights as positive samples, and $m$ links with the smallest weights as negative samples.
- Then, we construct feature vector for each (ordered??) pair of gene in $E_{sample}$, by simply concatenation of two gene expression vectors. For directed edge $e=(u, v) \in E_{sample}$, the feature vector is $\boldsymbol{x} _e=[\boldsymbol{g} _u; \boldsymbol{g} _v] \in \mathbb{R} ^{2p}$.
- The feature matrix is then $\boldsymbol{X} \in \mathbb{R} ^{2m \times 2p}$.


Algorithm
1. calculate a distance matrix $\boldsymbol{D} \in \mathbb{R} ^{2m \times 2m}$ for the feature vectors by Laplacian kernel

    $$
    D_{ij} = d(\boldsymbol{x}_i, \boldsymbol{x} _j) = \exp (-\sigma \left\| \boldsymbol{x} _i - \boldsymbol{x} _j \right\|_2 )
    $$

    where $\sigma=1$ in this paper.


2. project the training set onto the eigenvectors of the distance matrix, to obtain noise-removed features (why noise is removed/why this works?! see [35])

    - EVD: $\boldsymbol{D} = \boldsymbol{V} \boldsymbol{\Sigma} \boldsymbol{V}^{\top}$
    - Use the smallest $t$ eigenvectors, denoted $\boldsymbol{V} _t$
    - project data matrix $\boldsymbol{X}$ onto $\boldsymbol{V} _t$:

      $$\boldsymbol{C} = \boldsymbol{V} _t \boldsymbol{V} _t ^{\top}\boldsymbol{X} \in \mathbb{R} ^{2m\times 2p}$$

      each column of $\boldsymbol{C}$ is a noise-removed feature vector.

3. select important features from the noise-removed features, which then form a **cleaned** training set to be used to train supervised methods (SVM and NN in this paper)

    Specifically, compute column sum of $\boldsymbol{C}$, find the $k$ minimum values from $2p$ values (why minimum ??). Return these $k$ noise-removed feature vectors, denoted $\bar{\boldsymbol{C}} \in \mathbb{R} ^{2m \times k}$, termed 'important features', forming the cleaned training set. (in this paper $k=10$).


## Experiments

### Data

Three datasets in DREAM4 100-gene in silicon [13, 14].
- Each dataset contains 100 genes
  - For each gene, there are 10 times series, each time seires has 21 time points. Hence $\boldsymbol{g} \in \mathbb{R} ^{210}$ and $\boldsymbol{x} \in \mathbb{R} ^{420}$.
- Each dataset is associated with a gold standard file, representing the true regulatory relationships (directed) between two genes.

||Net1|Net2|Net3|
|-|-|-|-|
| Directed  | Yes  | Yes  | Yes  |
| Nodes   | 100  | 100  |  100 |
| True present links   | 176  | 249  |  195 |
| True missing links   | 9724  | 9651  |  9705 |

#### Training set

For each dataset,
1. run Inferetor to obtain a list of ordered gene pairs with weights
2. select
    - $m$ gene pairs with largest weights as positive examples
    - $m$ gene pairs with smallest weights as positive examples
3. construct
    - $2m$ feture vectors, as original training set $\boldsymbol{X}_{train} \in \mathbb{R} ^{2m \times 2p}$.
    - $2m$ labels, including $m$ number of 1's and $m$ number of -1's, denoted $\boldsymbol{y} \in \mathbb{R} ^{2m}$

#### Test set

For each dataset, we generated three sets of **testing data** $\boldsymbol{X} _{test_1}, \boldsymbol{X} _{test_2}, \boldsymbol{X} _{test_3}$
- Each testing set contains 50 randomly selected links (gene pairs) from the gold standard.
  - Among the 50 links, 25 are true present links and 25 are true missing links in the gold standard.
- 9 testing sets totally
- disjoint with $2m$ gene pairs in training set


### Pipeline

For each dataset,
1. Construct one training set $\boldsymbol{X} _{train}$ and three test sets $\boldsymbol{X} _{test_1}, \boldsymbol{X} _{test_2}, \boldsymbol{X} _{test_3}$ as described above.
2. Run proposed data cleaning algorithm on $\boldsymbol{X} _{train}$ to obtain cleaned training set $\bar{\boldsymbol{C}} \in \mathbb{R} ^{2m \times k}$, with label $\boldsymbol{y}$.
3. Train classifiers (SVM, DNN) on $\bar{\boldsymbol{C}}$.
4. Evaluate trained classifiers on $\boldsymbol{X} _{test_1}, \boldsymbol{X} _{test_2}, \boldsymbol{X} _{test_3}$.


### Evaluation

Consider three supervised classification algorithms
- SVM with various kernels
- DNN with weights initialized by deep belief networks (DNN_DBN)
- DNN with weights initialized by stack AutoEncoder (DNN_Auto)

Option of probability estimation was turned on in each algorithm.

Classification rule:

- SVM, DNN:
  - positive: prob > median prob
  - negative: prob < median prob

- Inferelator (unsupervised method on test sets)
  - positive: weight > median weight
  - negative: weight < median weight

Use averaged AUC across 9 testing sets as creiteria for comparison. (weird definition of AUC in eq.11)??

### Results

Compare SVM with various kernel (Fig.1)
- Linear kernel perfomrs the best among Gaussian, sigmoid, polynomial kernels.

Compare SVM with linear kernel, DBN, Inf (TABLE II)
- Net1: SVM_L, Net2: DNN_Auto, Net3: DNN_DBN.
- All three supervised methods perform better than the unsupervised method Inferelator.
- Overall, SVM_L performs better than DNN.
  - Possibly because this is a **binary** classification with **small** dataset.

Compare results w/ and w/o the proposed cleaning algorithm (Fig.2)
- AUC are smaller for SVM_L and DNNs.
  - SVM_L suffers the most when data is not cleaned.
- showing the effectiveness of our proposed data cleaning algorithm.

Compare different important feature dimension $k=20, 30, 40$ (Fig.3)
- SVM_L still the best for all $k$

Varying other hyperparameters give similar results.
- $m$: #sampled gene pairs
- $\sigma$: paramter in Laplacian kernel
- $t$: #eigenvectors


## Takeaway

### Ref of interest

Inferelator [18] [[PDF](https://genomebiology.biomedcentral.com/track/pdf/10.1186/gb-2006-7-5-r36.pdf)]
- why output **ordered** pairs??

Related noise-filtering algorithm by Ouyang et al. [35] [[PDF](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4720285/pdf/pone.0146925.pdf)]
- Ouyang's algorithm is designed for undirected networks, and employs EVD of Laplacian matrix
- In this paper, we consider directed networks (output of Inferelator are **ordered** gene pairs), hence Laplacian matrix is not symmetric, whose EVD involves complex numbers
- To solve this, use kernel function to construct a distance matrix $\boldsymbol{D}$.

### Questions

Robustness to order of genes
- For $m$ negative examples in training set, if we reverse the order (since their order does not matter), how will that affect the $k$ import feture vector?
- How about reversing order for $m$ positive orders? If we only care about interaction, but not care direction, then their order does not matter.
