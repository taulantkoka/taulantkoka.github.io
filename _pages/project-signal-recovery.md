---
title: "Cross-Channel Unlabeled Sensing"
permalink: /projects/signal-recovery/
layout: single
sidebar:
  nav: "projects"
---
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    TeX: { extensions: ["bm.js"] }
  });
</script>
`Signal Processing` · `Optimization` · `Sampling Theory` · `Sparse Recovery`

Traditional sampling theorems, from Shannon-Nyquist to Compressed Sensing, rely on a fundamental assumption: the correspondence between a sample and its index (or channel) is known and fixed. But what if this assumption breaks down?

## The Problem: Unlabeled Sensing

This project addresses **Cross-Channel Unlabeled Sensing (CC-ULS)**, a sub-problem of unlabeled sensing where multi-channel signals are observed through a permuted measurement process. Specifically, we consider a setup where, at each time point, the samples across $M$ channels are shuffled by an unknown permutation matrix $\Pi$.

Mathematically, we model this as:
$\bm y = \bm \Pi_{\mathbf{Q}} \mathbf{A} \bm \beta$
where $\mathbf{A}$ is a highly structured sensing matrix. While standard Unlabeled Sensing (ULS) theory provides recovery guarantees for i.i.d. Gaussian sensing matrices, these results do not apply to the structured matrices found in signal processing, such as those involving IDFT or Vandermonde structures.

## Technical Contributions

### 1. The Restricted Full Rank Property (RFRP)
To establish uniqueness results, I utilize the **Restricted Full Rank Property (RFRP)**. A subspace satisfies the RFRP if its dimension is preserved under any coordinate projection that keeps at least $K$ entries. 

One of the key theoretical results of this work is proving that certain structured matrices—specifically the product of an Inverse DFT matrix and a Vandermonde matrix ($W V$)—satisfy the RFRP. This proof relies on factorizing the sub-matrices into diagonal and **Cauchy matrices**, allowing us to derive closed-form expressions for their determinants.

### 2. Recovery over a Union of Subspaces
Moving beyond my initial Master's work, my recent research expands the CC-ULS framework to signals residing in a **union of subspaces**. This extension allows the model to handle significantly more complex signal structures and provides tighter theoretical bounds ($N \geq MK$) for unique reconstruction.

### 3. Sparse Signal Recovery
I have extended these results to continuous-time sparse signals (streams of Diracs). By exploiting the shift-invariant properties of the Fourier Series, we can recover both the locations and weights of the spikes even when the measurement channels are completely mixed.

## Real-World Applications

- **Whole-Brain Calcium Imaging**: In imaging freely moving organisms (like larval zebrafish), movement often disrupts the mapping between physical neurons and recorded traces. CC-ULS provides a framework to "de-shuffle" these traces and reconstruct true neuronal activity.
- **Multi-Target Tracking**: Resolving data association in scenarios where trajectories cross or occlusions occur, making it difficult to maintain the identity of individual targets.

## Publications

- **T. Koka**, M. C. Tsakiris, B. Béjar Haro, M. Muma. *Cross-Channel Unlabeled Sensing over a Union of Signal Subspaces.* **IEEE ICASSP**, 2025.
- **T. Koka**, M. C. Tsakiris, M. Muma, B. Béjar Haro. *Shuffled multi-channel sparse signal recovery.* **Signal Processing** (Elsevier), 2024.