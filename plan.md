 # Plan

## Overview of the Analytic FMM

### Motivation

- Read introduction in Greengard thesis for inspiration for problem setting.
- Read other introductory texts on FMM (short course on FMM, multipoles in Griffith)
- To see how they ^ introduce the algorithm, and the sources they use.
- Instead of dedicated analysis section may want to instead write about three
step procedure to introduce the efficacy of the algorithm?

Figures required:
 None

### Algorithm structure

- Explain partitioning into heirarchical tree
- List steps of pseudo algorithm, write full loop in the appendix to save space

Figures required:

Figure demonstrating m2m vs m2l vs l2l differences in 3D, can make in powerpoint.


### Analysis

- Add full analysis to the appendix OR to save time refer to the original paper.
- Add comment on computational complexity, but compare to practical bottlenecks
that result from coefficient computations and impracticality of reimplementation
for each each kernel type.
- Practical benefits of `Kernel Free' implementation that mirrors original
spec.

Figures required:

None

## Overview of KIFMM

### Motivation

- Explain equivalent density concept. Explain trade off of analyticity for error
but get easy implementation

Figures required:
 None

### Algorithm structure

- Draw parallels to AFMM (Tree/Main Loop).
- Highlight differences in the operators based on the equivalent density concept.

Figures required:
    Similar to the ones in the previous section, but with grids.
    can plot these.

### Analysis

- Get same computational complexity (Justify from L Ying), different practical bottlenecks. Highlight new tradeoffs, compare to old ones and justify!

Figures required:
    None

## Strategy for Practical Implementation

- Comment on section outline. Comment on the efficiency savings that different implementations could benefit from in terms of real software.


Figures required:
    None

### Bottlenecks

- List and analyse main mathematical bottlenecks
    - Large number of similar M2L computations.
        - SVD Compression.
    - Pseudo-Inverse computation of gram matrix.
        - Operator Caching + Reusing surfaces

- List and analyse main software/hardware bottlenecks
    - Large number of similar M2L computations.
        - Multiprocessing

Figures required:
    None

### Space Filling Curves

- History of usage (brief, link to original papers).
- Explain concept (hashing, tree traversal)
- Explain usefulness (vector representation, ready for optimization)

Figures required:
    Morton z-encoding figure to illustrate point.

### Operator Caching and Multiprocessing

- Comment on dependence between particles and operators required to be computed.

Figures required:
    None

### SVD Compression

- Comment on FFT compression already in use by ExaFMM, PVFMM etc. Comment on lack of software implementations of SVD based approach in 3D.
- For now, derive basic SVD caching idea applied to M2L operators.

Figures required:
    None

### Software Design

- Comment briefly on software design principles used in this project. OOP from the start. The requirement for simple objects. Debugging as well as numerics requirements.
- Namely the separation of concerns, dependency injection, and open-close principle.

## Experiments and Results

### Benchmarking

- Estimate cost savings from parallelization steps.

- Compare experimental results for FMM vs Direct computation as a function of number of particles.

Figures required:
    - KEY RESULT: Benchmarking figure as a function of N - particles
    - IMPORTANT TO MEASURE:
        Cost benefit of SVD Compression for some random matrices.
    - relative error plot - vars of tree depth/expansion order.

### Optimum SVD Parameter

Figures required:
    - relative error plot - fixed tree depth/expansion order vs SVD tolerance.

## Conclusion

- 3D lib with some optimisations
- results of benchmarking

### Future Work

- Parallel tree construction
- An adaptive algorithm
- Offloading M2L computation to specialized hardware
- More robust SVD compression, randomized compression.
- Taking more check points than multipole/local points for more robust pseudo-inverse.
