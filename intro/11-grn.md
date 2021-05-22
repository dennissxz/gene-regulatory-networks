
# Gene Regulatory Networks

Problem

- Though experimentally infeasible, can we construct the gene regulatory (activation or repression) networks as a problem of network inference, given measurements sufficiently reflective of gene regulatory activity?

Definition
- **Genes** are sets of segments of DNA that encode information necessary to the proper functioning of a cell.
- such information is utilized in the **expression** of genes, whereby biochemical products, in the form of RNA or proteins, are created
- The **regulation** of a gene refers to the control of its expression.
- A gene that plays a role in controlling gene expression at transcription stage (DNA is copied to RNA) is called a **transcription factor** (TF), and the genes that are controlled by it, gene **targets**.
- The problem of inferring regulatory interactions among genes in this context refers to the identification of **TF/target gene pairs**.

Measurements
- The relative levels of RNA expression of genes in a cell, under a given set of conditions, can be measured efficiently on a genome-wide scale using **microarray** technologies.
- In particular, for each gene $i$, the vertex attribute vector $\boldsymbol{x}_i \in \mathbb{R} ^m$ typically consists of RNA relative expression levels measured for that gene over a compendium of $m$ experiments, e.g. changes in pH, growing media, heat, oxygen concentrations, and genetic composition.

Observation
- Due to the nature of the regulation process, the expression levels of TFs and their targets often can be expected to be highly correlated with each other
- Given that many TFs have **multiple** targets, including other TFs, the expression levels among many of the TFs can be expected to be highly correlated as well
- Such correlations are evident in the image plot, where the order of the
genes (rows) and experiments (columns) has been chosen using a cluster analysis, to enhance the visual assessment of correlation.

<img src="../imgs/graph-gene-exp.png" width = "70%" alt=""/>

Image representation of 445 microarray expression profiles collected for E. coli, under various conditions, for the 153 genes that are listed as known transcription factors (TF) in the RegulonDB, using blue-organge scale. [Kolaczyk 2009]
database.

**Challenge**
- A TF can actually be a target of another TF. And so direct correlation between measurements of a TF and a gene target may actually just be a reflection of the regulation of that TF by another TF. Sol: use partial correlation.

Methods and Results
- testing null correlation to construct undirected graph. See SAND pg.221-223
- All methods have high precision and low recall. Faith et al. [SAND 140] have argued that the low recall is due primarily to limitations in both the number and the diversity of the expression profiles produced by the available experiments.
- Takeaway: high precision model is still good for prediction purpose, though not good at recover old true labels (low recall).
