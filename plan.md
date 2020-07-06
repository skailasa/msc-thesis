 # Plan



## Overview of the Analytic FMM

### Motivation

- Read introduction in Greengard thesis for inspiration for problem setting.
- Read other introductory texts on FMM (short course on FMM, multipoles in Griffith)
- To see how they ^ introduce the algorithm, and the sources they use.
- Instead of dedicated analysis section may want to instead write about three
step procedure to introduce the efficacy of the algorithm?

Figures required:


### Algorithm structure

- Explain partitioning into heirarchical tree
- List steps of pseudo algorithm, write full loop in the appendix to save space

Figures required:


### Analysis

- Add full analysis to the appendix OR to save time refer to the original paper.
- Add comment on computational complexity, but compare to practical bottlenecks
that result from coefficient computations and impracticality of reimplementation
for each each kernel type.
- Practical benefits of `Kernel Free' implementation that mirrors original
spec.

Figures required:


## Overview of KIFMM

### Motivation

- Explain equivalent density concept. Explain trade off of analyticity for error
but get easy implementation

Figures required:


### Algorithm structure

- Draw parallels to AFMM (Tree/Main Loop).
- Highlight differences in the operators based on the equivalent density concept.

Figures required:


### Analysis

- Get same computational complexity (Justify from L Ying), different practical
bottlenecks. Highlight new tradeoffs, compare to old ones and justify!

Figures required:


## Strategy for Practical Implementation

- Comment on section outline. Comment on the efficiency savings that different
implementations could benefit from in terms of real software.


Figures required:


### Bottlenecks

- List and analyse main mathematical bottlenecks
- List and analyse main software/hardware bottlenecks
- Provide numerical/mathematical justification for urgency/priority of bottlenecks
- Suggest solutions, and describe solutions attempted in this paper

Figures required:


### Space Filling Curves

- History of usage (brief, link to original papers).
- Explain concept (hashing, tree traversal)
- Explain usefulness (vector representation, ready for optimization)

Figures required:


### Operator Caching

- Comment on dependence between particles and operators required to be computed.
- Comment on potential computational waste on `empty' operators.

Figures required:


### SVD Compression

- Comment on FFT compression already in use by ExaFMM, PVFMM etc. Comment on lack of software implementations of SVD based approach in 3D.
- For now, derive basic SVD caching idea applied to M2L operators.
- Leave space (5-10 pages) for alternative SVD based approaches which may be
explored in the future.

Figures required:




