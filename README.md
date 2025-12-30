# AML-Genomic-Subtyping-with-Graphs-Causality

This project explores acute myeloid leukemia (AML) genomic subtypes using a graph-based machine learning approach, inspired by the paper:

“Machine learning integrates genomic signatures for subclassification beyond primary and secondary acute myeloid leukemia”
Blood (2021) https://ashpublications.org/blood/article-abstract/138/19/1885/476049

Instead of using traditional supervised classifiers alone, this project takes a network-driven perspective to understand how mutations co-occur, cluster, and define biological AML subtypes.


💡 Core Idea

AML is not driven by single mutations, but by combinations of cooperating mutations that define distinct disease entities.

This project answers three key questions:

Which mutations tend to appear together in the same patients?

Can we automatically identify mutation communities (subtypes)?

Which mutations show strong positive or negative (exclusive) relationships?


📊 Data

Source: Simulated AML mutation matrix derived from TCGA-LAML (Genomic Data Commons, NCI)

Patients: ~3000

Genes analyzed (binary: mutated / not mutated):

NPM1, FLT3-ITD, DNMT3A

RUNX1, ASXL1, SRSF2, U2AF1

TP53, Complex Karyotype

IDH1, IDH2

CEBPA (biallelic)

⚠️ Data are simulated for methodological demonstration, not direct clinical inference.


🔗 1. Co-occurrence Network (Graph-Based View)
What does the graph represent?

Nodes (dots) → Gene mutations

Edges (lines) → Two mutations appear together in many patients

Edge thickness → Number of patients sharing both mutations

Threshold applied → Only draw an edge if >80 patients have both mutations

co_occurrence = aml_df[GENES].T.dot(aml_df[GENES])

This matrix counts how many patients carry both mutation A and mutation B.


Why use a threshold (e.g. >80 patients)?

Small co-occurrence counts can occur by chance

Larger counts are more biologically meaningful

Thresholding reduces noise and improves interpretability

Trade-off: rare but real relationships may be missed — this is intentional to focus on dominant AML biology.


2. Automatic Mutation Community Detection

To identify groups of mutations that tend to appear together, we applied modularity-based community detection:

communities = greedy_modularity_communities(G)

Resulting mutation communities:

Module 1
Core AML signaling / epigenetic mutations
(NPM1, FLT3-ITD, DNMT3A, IDH2, RUNX1, splicing genes)

Module 2
Adverse-risk AML
(TP53, Complex Karyotype, ASXL1)

Module 3
Isolated metabolic subtype
(IDH1)

These communities emerge automatically, without predefined labels — showing that graph structure alone recovers known AML subtypes.


🧠 3. Raw Co-occurrence Counts (Transparency)

To maintain interpretability, the project also prints:

The full co-occurrence matrix

A ranked table of significant mutation pairs

Example:

Gene 1	Gene 2	Patients
NPM1	DNMT3A	472
NPM1	FLT3-ITD	458
TP53	Complex Karyotype	367
RUNX1	SRSF2	385

These numbers align strongly with established AML biology.


🎨 Visualization (What You See in the Figure)
Node Colors (Biological Meaning)

🔵 Sky blue → NPM1 / FLT3-ITD / DNMT3A / IDH1/2
(Signaling & epigenetic AML)

🟢 Light green → RUNX1 / SRSF2 / U2AF1 / ASXL1
(Splicing / transcription – secondary AML)

🔴 Salmon → TP53 / Complex Karyotype
(Adverse-risk AML)

🟣 Violet → CEBPA biallelic
(Favorable-risk AML)

Edge Interpretation

Black edges → Strong co-occurrence

Thicker edges → More patients share both mutations

Dense clusters → Biological AML subtypes


⚠️ Limitations

Cross-sectional data → associations, not true temporal causality

Thresholding may miss rare subtypes

Simulated dataset used for demonstration



