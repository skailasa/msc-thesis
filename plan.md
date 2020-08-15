 # Plan

## Overview of the Analytic FMM

#### Other Tasks:

- Read GPU Computing Gems chapter p113 and make notes

### Motivation

- Introduce with paradigm problem of electrostatics
- Greengard adapted discussion on how potentials are generically structured
- Formulate calculation as integral
- demonstrate naive complexity.
- GPU Gems gives examples of other applications, this allows you to explain why
this is an interesting an important field of study.

Figures required:
- None

### Algorithm structure
- Derivation of the multipole/local expansion from a distribution of charge, already done.
- [Diagram] Different types of expansions
- The analytic expressions for the shift operators, in the appendix, but need more exposition to tie them together.
- Explain how expansion order can be chosen, resulting in rigorous error bounds.
- List steps of pseudo algorithm, write full loop in the appendix to save space
- Emphasise how recursive tree traversal allows you to reuse calculations made
at previous levels.
- [Diagram] Algorithm flow

Figures required:
- Figure demonstrating m2m vs m2l vs l2l differences in 3D, can make in powerpoint.
- Algorithm Flow diagram like in GPU Computing Gems p116

### Analysis

- Extend already written comment on complexity to make it easier to read.
- Emphasise the huge cost benefit of the algorithm using the top 10 algos paper
- Talk about the practical challenges of implementation.
    - namely, requirement for efficient data structures to store/compute the tree.
        - i.e. briefly mention Morton encoding.
        - briefly mention need for vectorization to apply to modern hardware
    - additionally, the potential to precompute expansion coefficients to save time.
        - software implementations can take advantage of parallel processing to save time here.


Figures required:
- None

## Overview of KIFMM

#### Other tasks

- Build a cursory understanding of analysis of KIFMM from Ying to demonstrate
that the computational complexity is the same as analytic FMM.
- Find justification for ill-conditioning of this integral equation formulation
    - from that Cambridge integral equation textbook ... The Numerical Solution of Integral Equations of the Second Kind

### Motivation

- Requirement for a new software implementation for each kernel is unproductive
- KIFMM resolves this by relying on kernel evaluations rather than expansions.
- Allows for an kernel-agnostic software implementation.

Figures required:
 None

### Algorithm structure

- Draw parallels to AFMM (Tree/Main Loop).
- Highlight differences in the operators based on the equivalent density concept.

Figures required:
- Figures demonstrating the concept of check/equivalent surface for the three
main operators. 3D is preferable.

### Analysis

- Get same computational complexity (Justify from L Ying),
- Different practical bottlenecks.
    - Inversion of ill-conditioned kernel matrix required for each step.
        - Integral equations textbook gives good justification on why this matrix
        is ill conditioned.
        - Addressed via operator pre-computation.
        - this results in other bottleneck of multi-processing these calculations too.
    - accelleration of m2l is done via fft in original paper in 3D, we use low rank
    SVD approximation to reduce the number of M2L computations.
- Some bottlenecks are the same as before
    - Tree construction/implementation

Figures required:
- None

## Strategy for Practical Implementation

### Bottleneck Analysis

- Comment on bottlenecks already mentioned
    - operator pre-computation and caching, m2l acceleration, efficient trees

- Comment on additional software design bottlenecks
    - Comment on overhead introduced by Python vs ExaFMM's C++ implementation
        - Justify why this is fine.
    - requirement for vectorized data of different formats, hence small number
    of simple objects.
    - requirement for design around parallelization of loops via tools like numba and numexpr.
    - requirement of modularity in order to be compatible with future extensions
    that would use GPUs
    - requirement for maintainability/testability in the face of a complex
    recursive algorithm with stability issues.
        - requirement for separation of FMM logic from data manipulation code.
        - and data manipulation code from mathematical operations.
        - Safety objects/Expected return types

### Space Filling Curves

- History of usage (brief, link to original papers).
    - PVFMM Paper Malhotra et al has good links to origin of this method.
- Explain morton encoding concept/algorithm.
    - Explain implementation via bitwise operators.

- Explain usefulness (vector representation, ready for optimization)

- Explain how tree construction relies on these curves, and how this is optimised
via numba
- Also briefly comment on how parallel tree construction could be done
in the future.

Figures required:
    - Morton z-encoding figure to illustrate point.
        - Look at the one on wikipedia for inspiration.

### Operator Caching and Multiprocessing

- Comment on hardware that this project was completed on, vs what this code
could be run on.
- Comment on the (process) level of parallelism taken advantage of vs other levels of parallelism
available in this computation.
- Remark on the additional costs in the KIFMM of having to compute inverse/surfaces
each step of the algorithm up and down.
- Comment on parallelisable steps in operator processing, and how multiprocessing helps
address these in the first instance.
- Comment on how the operators are dependent on the particles, and how numba helps
to relieve some computations via njit caching.

Figures required:
- None

### SVD Compression

- Comment on FFT compression already in use by ExaFMM, PVFMM etc.
- Comment on lack of software implementations of SVD based approach in 3D.
- For now, derive basic SVD caching idea applied to M2L operators.
- Justify using Trefethan how SVD can provide a best approximation of an operator.
- Discuss how we can take fewer singular values and still capture most of the action
of an opertor.
- Briefly mention more advanced SVD compression techniques, randomised SVD.

Derivations:
- Low Rank SVD derivation
- Brief justification of best approximation, link to proof.
- Idea of trading off fidelity of SVD compressed operator for reduced overhead in computation.

Figures required:
- None

### Software Design

- Comment on techniques and patterns followed:
- The requirement for simple objects. Debugging as well as numerics requirements.
- Namely the separation of concerns, dependency injection, and open-close principle.

- SOC
    - modularity of numerical vs data vs logic/algorithmic code
- dependency injection crucial for separating logic and data
- open-close
    - crucial for extensiblity of functionality.
- OOP in general crucial for offering a few heavyweight objects for testing.
    - requirement for safety objects for unit testing.

- Comment on how future versions will iterate on design, introducing more differential
testing as numerical testing is not very accurate.


## Experiments and Results

- Specify a benchmarking tree topology that is easy to compute a lot of
- Random particle distribution

### Benchmarking

- Estimate cost savings from each parallelization step
    - Multiproc
    - SVD compression
    - numba

- Compare experimental results for FMM vs Direct computation as a function of number of particles.
    - Need to plot the computational complexity.

Figures required:
    - KEY RESULT: Benchmarking figure as a function of N - particles

### Optimum SVD Parameter

- Experiment with adjusting this parameter for a fixed tree topology/particle
distribution.
- Particle distributions to try
    - Random
    - Two blobs
    - Many blobs
SVD parameter will reflect particle distributions is my suspicion.

Figures required:
    - relative error plots

## Conclusion

- Summarise contribution of this project.
    - 3D KIFMM Lib
    - Some parallel/vectorized features
        - multiproc
        - m2l svd compression
        - njit trees/tree operations
        - extensible, well tested, software design.

### Future Work

- Parallel tree construction
- An adaptive algorithm
- Offloading M2L computation to specialized hardware
- More robust SVD compression, randomized compression.
- Multiproc svd compression library
- Taking more check points than multipole/local points for more robust pseudo-inverse.
