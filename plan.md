 # Plan

## Overview of the Analytic FMM

#### Other Tasks:

- Read GPU Computing Gems chapter p113 and make notes

### Motivation

- Introduce with paradigm problem of electrostatics
- Greengard adapted discussion on how potentials are generically structured
    - This introduces the idea that potentials from particles in far field can be compressed.
- Formulate calculation as integral/summation with kernel.
- mention naive complexity.
- GPU Gems gives examples of other applications, this allows you to explain why
this is an interesting an important field of study.

- Explain concept of multipole expansion of a charge distribution using Griffiths
based derivation for Laplace kernel
- The key point is that it's an exact expansion, and can be truncated for tunable
precision - which could be application/implementation dependent.
- This leads to rigorous error bounds - refer to Greengard thesis, don't have to be
specific with this as the analysis isn't key for me.
- Write local expansion, and refer to appendix for shift operators and mention that
they are there because it's not the focus of this study.
- Derivation of the multipole/local expansion from a distribution of charge, already done.

- [Diagram] Different types of expansions (local/multipole)


### Algorithm structure

- Explain how the FMM uses these expansions and their shifts to compress potentials
in the far field for their near field evaluations. This discussion is very concise
in GPU Gems, so read this before trying to do this and use as a core reference.

- Explanation of how tree is used, and why

- The analytic expressions for the shift operators, in the appendix, but need more exposition to tie them together.

- Re-emphasise how expansion order can be chosen, resulting in rigorous error bounds.

- List steps of pseudo algorithm, write full loop in the appendix to save space

- [Diagram] Algorithm flow similar to the GPU Gems book, reference this.

Figures required:
- Figure demonstrating m2m vs m2l vs l2l differences in 3D, can make in powerpoint.

- Algorithm Flow diagram like in GPU Computing Gems p116


### Analysis

- Extend already written comment on complexity to make it easier to read.

- Emphasise the huge cost benefit of the algorithm using the top 10 algos paper to hammer home the technical advance that it represented.

- Talk about the practical challenges of implementation of Analytic FMM
    - Different kernel will require different coefficient evaluation software to be written, this is a sigfnificant programming overhead for maintainence/implementation.
    - Requirement for efficient data structures to store/compute the tree IRL.
        - i.e. 3D Carts need to be vectorised efficiently, briefly mention Morton encoding and future chapters that will cover this.
    - Inherent parallelism in so many of the computations
        - any operator evaluations at a given level for example (M2l, M2M, P2M, L2P) etc etc.
        - therefore any of the pre-computations too.
        - JIT compilation of data structures.
        - efficient sharing of data across process level parallelism.

- Basically many ways to proceed with implementation. However the challenge will be to ensure that the software is easily extensible and testable. This is complicated by the recursive nature of the algorithm itself.

Figures required:
- None

## Overview of KIFMM

#### Other tasks

- Build a cursory understanding of analysis of KIFMM from Ying to demonstrate
that the computational complexity is the same as analytic FMM.

- Find justification for ill-conditioning of this integral equation formulation
    - from that Cambridge integral equation textbook ... The Numerical Solution of Integral Equations of the Second Kind

### Motivation

- Requirement for a new software implementation for each kernel is unproductive and hard to maintain as a developer.

- KIFMM resolves this by relying on kernel evaluations rather than expansions.

- Approximately the same algorithmic structure, the same algorithmic complexity + ease of implementation in comparison to the AFMM.

Figures required:
None


### Algorithm structure

- Introduction to the least squares problem following Ying. i.e. including concept of surfaces and how they're used.

- [DIAGRAM] of surfaces for three main operators in 3D.

Figures required:

- Figures demonstrating the concept of check/equivalent surface for the three
main operators. 3D is preferable.

### Analysis

- Get same computational complexity (Justify from L Ying), but doesn't even have to be super long - just the specific reasons as to why it's bounded by the same complexity i.e. something based on how the kernel evaluations are of lower/same complexity as calculating the coefficient products in analytic FMM.

- Some differing practical bottlenecks.
    - Inversion of ill-conditioned kernel matrix required for each step.

- Comment on the numerical instability of this, use Cam Textbook to justify this mathematically.

- solved via a singular value decomposition and filtering out sing vecs with low sing vals. and calculation re-used in order to calculate all operators at all levels and just scaled for different positions and stored.

- commment on how the m2l can be accellerated because of the structure of its surfaces and how we do it different to the original paper

Figures required:
- None

## Strategy for Practical Implementation

### Bottleneck Analysis

- Comment on bottlenecks already mentioned and how these are tackled in this chapter/software implementation
    - operator pre-computation and caching
        - multiprocessing + caching
    - m2l acceleration
        - SVD Low Rank Compression
    - Efficient Trees
        - Hilbert Encoding, JIT Compilation of Hilbert operators.

- Comment on additional software design bottlenecks

- Comment on overhead introduced by Python vs ExaFMM's C++ implementation
    - Justify why this is acceptable, introduce JIT more formally.

- Comment on requirement for extension to vectorized data of different formats, hence preference for a small number of simple objects.

- Comment on requirement of modularity in order to be compatible with future extensions
    that would use GPUs for operator evaluations.

- Finally requirement for maintainability/testability in the face of a complex
    recursive algorithm with potential stability issues from inversion.
        - leads to requirement for separation of FMM logic from data manipulation code.
        - and leads to requirement to separate data manipulation code from mathematical operations.
        - Safety objects/Expected return types

Figures Required:
- None

### Space Filling Curves

- Brief comment on History of usage (brief, link to original papers).
    - PVFMM Paper Malhotra et al has good links to origin of this method.

- Explain usefulness (vector representation, ready for optimization)

- Explain hilbert encoding concept/algorithm
    - Explain implementation via bitwise operators.
        - Explain potential for caching of these for very fast access.

- Explain how tree construction relies on these curves, and how this is optimised
via numba

- Also briefly comment on how parallel tree construction could be done
in the future.

- [DIAGRAM] of Z-Encoding.

- Comment finally on practical implementation via library.

Figures required:
    - Morton z-encoding figure to illustrate point.
        - Look at the one on wikipedia for inspiration.

### Operator Caching and Multiprocessing

- Comment on re-use of surfaces/inverse computation, and why this is necessary in KIFMM implementations.

- Comment on real values used for the surfaces, and where the justification for this comes from. i.e. PVFMM paper

- Comment on how the operators are dependent on the particles, and how this computation has been done in the real implementation. i.e. shifting a surface around the tree. scaling the operators for l2l and m2l to save time.

m2l operator computed densely, leads to process level parallelism usage, but how this can be shifted to dedicated compute device i.e. GPU in the future further accellerating.

- Comment on the (process) level of parallelism taken advantage of vs other levels of parallelism available in this computation. and how multiprocessing helps
address these in the first step towards a highly parallel implementation.

- Comment finally on practical implementation via cli, and the required data formats for the library to work. Comment on choice for HDF5 for the future of the project.

Figures required:
- None

### SVD Compression

- Comment in more detailed fashion on FFT compression already in use by ExaFMM, PVFMM etc. and why this works (surfaces)

- Comment on lack of software implementations of SVD based approach in 3D.

- Derive SVD caching idea applied to M2L operators.

- Justify using Trefethan how SVD can provide a best approximation of an operator.

- Discuss how we can take fewer singular values and still capture most of the action
of an operator.

- Briefly mention more advanced SVD compression techniques, randomised SVD.

- Comment on practical implementation via cli upon precomputed operators.

- Hint at experiments that will be done in the next section.

Derivations:
- Low Rank SVD derivation

- Brief justification of best approximation, link to proof.

- Idea of trading off fidelity of SVD compressed operator for reduced overhead in computation.

Figures required:
- None

### Software Design

- Comment on Overhead of using Python vs Compiled languages C++/Fortran
    - Why trading efficiency for productivity is good.
    - Short discussion on JIT techniques like numba and numexpr
    - Comment on locations in code (tree creation/manipulation) where JIT is used.
    - Simple benchmark number for speedup offered by numba for a simple piece of
    numerical code.
    - programmer efficiency of thinking in familiar loops.

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

- Random particle distribution (10k? 100k?) Depends on how long it takes try both and see if 1e5 reasonably fast, otherwise use 1k/10k.

### Benchmarking

- Estimate cost savings from each parallelization step
    - Multiproc
    - SVD compression
    - numba

- Compare experimental results for FMM vs Direct computation as a function of number of particles for the LAPLACE KERNEL!!!!. Justify usage of laplace kernel (paradigm, easy etc.)

- [DIAGRAM] Need to plot the computational complexity.

- Understand cost of computation via pyexafmm compared to state of the art exafmm-t.
    - Comment on where the slowness comes from
        - no parallelism for operator evaluations
        - no parallelism for memory sharing
            - lots of copying of same data.
        - subtoptimal data formats for m2l - lots of loading.
        - suboptimal size of data structures lead to loading from non-cpu memory - which is slow - could be optimized via numexpr etc in the future or more intricate chunking of memory.
    - These are justifiable from the time constraints on full-time development.

Figures required:
    - KEY RESULT: Benchmarking figure as a function of N-particles

### Optimum SVD Parameter

- Experiment with adjusting this parameter for a fixed tree topology/particle
distribution.

- Particle distributions to try
    - Random
    - Two blobs
    - Many blobs

SVD parameter will reflect particle distributions is my suspicion.

- [DIAGRAM] Relative error plots.

Figures required:
    - relative error plots

## Conclusion

- Contributions
    - The first iteration of the 3D (somewhat) parallel KIFMM lib in a popular interpreted language.
    - well tested and extensibly designed software.

- Limitations
    - hardware for benchmarking larger data sets.
    - level of parallelism for speed.

- Future
    - multiple levels of parallelism (data/process/cpu etc) available, they should be explored and optimised.
    - implementation of new kernels.
    - implementation of gradients for benchmarking with open source standards
    - reduction in boiler plate code for data loading etc, in terms of design.
    - more integration tests due to complexity.
    - Parallel tree construction
    - An adaptive algorithm
    - Offloading M2L computation to specialized hardware
    - More robust SVD compression, randomized compression.
    - Multiproc svd compression library
    - Taking more check points than multipole/local points for more robust pseudo-inverse.
