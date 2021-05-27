# Gene Regulatory Networks

Statistical Analysis of Network Data: Methods and Models. Kolaczyk, Eric D. [[Link](https://www.springer.com/gp/book/9780387881454)].


Definitions

- **Genes** are sets of segments of DNA that encode information necessary to the proper functioning of a cell.
- such information is utilized in the **expression** of genes, whereby biochemical products, in the form of RNA or proteins, are created
- The **regulation** of a gene refers to the control of its expression.
- A gene that plays a role in regulating gene expression at transcription stage (DNA is copied to RNA) is called a **transcription factor** (TF), and the genes that are controlled by it, gene **targets**.
- The regulatory relation can be activatory or inhibitory (aka repression).
- The problem of inferring regulatory interactions among genes in this context refers to the identification of **TF/target gene pairs**.

Measurements
- The relative levels of RNA expression of genes in a cell, under a given set of conditions, can be measured efficiently on a genome-wide scale using **microarray** technologies.
- In particular, for each gene $i$, the vertex attribute vector $\boldsymbol{x}_i \in \mathbb{R} ^m$ typically consists of RNA relative expression levels measured for that gene over a compendium of $m$ experiments, e.g. changes in pH, growing media, heat, oxygen concentrations, and genetic composition.

Observation
- Due to the nature of the regulation process, the expression levels of TFs and their targets often can be expected to be **highly correlated** with each other
- Given that many TFs have **multiple** targets, including other TFs, the expression levels among many of the TFs can be expected to be highly correlated as well
- Such correlation can be represented by a bipartite graph, $G = (V_{TF}, V_{target}, E)$
  - edges exists between $v_{TF}$ and $v_{target}$, as well as among $V_{TF}$.
  - some study consider directed edges, some consider undirected (interaction).
- Such correlations are evident in the image plot, where the order of the
genes (rows) and experiments (columns) has been chosen using a cluster analysis, to enhance the visual assessment of correlation.

:::{figure} graph-gene-exp.png
<img src="../imgs/graph-gene-exp.png" width = "80%" alt=""/>

Image representation of 445 microarray expression profiles collected for E. coli, under various conditions, for the 153 genes that are listed as known transcription factors (TF) in the RegulonDB database, using blue-organge scale. [Kolaczyk 2009]

:::

Problem

- Though experimentally infeasible, can we construct the gene regulatory (activation or repression) networks as a problem of network inference, given measurements sufficiently reflective of gene regulatory activity?

Challenge
- A TF can actually be a target of another TF. And so direct correlation between measurements of a TF and a gene target may actually just be a reflection of the regulation of that TF by another TF.

  $$\begin{aligned}
  \text{TF} _1 \quad &\longrightarrow \text{TF} _2 \\
  \ddots &\qquad \swarrow \\
  & \text{target}  \\
  \end{aligned}$$

  Solution: use partial correlation.


Intro: correlation-based methods
- Conduct hypothesis testing of null correlation to construct undirected graph. See SAND pg.221-223
- All correlation-based methods there have high precision and low recall. Faith et al. [SAND 140] have argued that the low recall is due primarily to limitations in both the number and the diversity of the expression profiles produced by the available experiments.
- Takeaway: high precision model is still good for prediction purpose, though not good at recover old true labels (low recall).

Other challenges
- limited data with gold standard labels
- Mixed regulatory relation may exist

  :::{figure}
  <img src="../imgs/mix-reg.png" width = "50%" alt=""/>

  Mixed regulation
  :::
