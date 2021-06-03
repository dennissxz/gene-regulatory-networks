# Datasets

## Limitation

[Reference](bio-net-dl).

Dataset limitation
- curated GRN datasets are not yet available or are difficult to obtain for a large number of organisms
- gene expression data are intrinsically noisy and therefore not ideal for training models.

For this reason, GRNs are mostly analyzed with unsupervised methods, since supervised techniques, and deep learning in particular, require a large number of well annotated samples in order to be effective.


## DREAM4

Greenfield A, Madar A, Ostrer H, Bonneau R: DREAM4: combining genetic and dynamic information to identify biological networks and dynamical models. PloS one 2010, 5(10):e13397.

Three datasets in DREAM4 100-gene in silicon.
- Each dataset contains 100 genes
  - For each gene, there are 10 times series, each time seires has 21 time points. Hence $\boldsymbol{g} \in \mathbb{R} ^{210}$ and $\boldsymbol{x} \in \mathbb{R} ^{420}$.
- Each dataset is associated with a gold standard file, representing the true regulatory relationships (directed) between two genes.

||Net1|Net2|Net3|
|-|-|-|-|
| Directed  | Yes  | Yes  | Yes  |
| Nodes   | 100  | 100  |  100 |
| True present links   | 176  | 249  |  195 |
| True missing links   | 9724  | 9651  |  9705 |

## DREAM5

Marbach D, Costello J, Ku«ffner R, et al. Wisdom of crowds for robust gene network inference. Nat Methods 2012;9(8): 796–804. [[DOI](https://www.nature.com/articles/nmeth.2016)]

[Challenge Description](https://www.synapse.org/#!Synapse:syn2787209/wiki/70350). Net 2 was not used in the final scoring..

| Network | Organism | #Genes | #TF | #Targets | #Links | #Samples | #Avg degree per TF |
|-|-|-|-|-|-|-|-|
| Net 1  | in silico  | 1643  | 195 | 1448 |4012|805|20.57|
| Net 2   | S. aureus  | 2810  |  99 | 2711 | |160|
| Net 3 | E. coli  | 4511  | 334 | 4177 | 2066 | 805| 6.19|
| Net 4 | S. cerevisiae  | 5950  | 333 | 5617 | 3940 | 536| 11.83|

## Simulators

### GeneNetWeaver

#### Installataion

[Website](http://gnw.sourceforge.net/genenetweaver.html). Need to install Java. If your browswer does not lauch the software, you may download [gnw-3.1.2b.jar](https://github.com/tschaffter/gnw/blob/master/gnw-3.1.2b.jar) and double click it to launch the software locally.

#### Steps

- extract **subnetworks** from known interaction networks (E. coli and S cerevisiae networks)
- simulate transcription and translation
- uses a set of ordinary differential equations describing chemical kinetics to generate expression data for experiments
  - knockout: expression value of each gene is set to 0 in turn (hence #exp = #genes)
  - knockdown: halved
  - multifactorial: expression levels of a small number of genes are perturbed by a small random amount.

#### Cons

Sample size = network size

### SynTReN

Similar but older simulator, faster than Gene NetWeaver, allows one to vary the sample number independently of the network size.

#### Steps

- extract E coli and S cerevisiae
- simulate transcription only
- simulate multifactorial only
