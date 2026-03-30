---
title: "Cross-Channel Unlabeled Sensing"
permalink: /projects/signal-recovery/
layout: single
mathjax: true
sidebar:
  nav: "projects"
toc: true
---
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    TeX: {
      extensions: ["bm.js", "AMSmath.js", "AMSsymbols.js"]
    },
    tex2jax: {
      inlineMath: [ ['$','$'], ["\\(","\\)"] ],
      displayMath: [ ['$$','$$'], ["\\[","\\]"] ],
      processEscapes: true
    }
  });
</script>
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

# When Your Labels Get Shuffled {: .no_toc}

## Recovering Multi-Channel Signals with Unknown Sample-Channel Assignments {: .no_toc}

**Taulant Koka · 2022–2025 · [Signal Processing (Elsevier)](https://doi.org/10.1016/j.sigpro.2024.109579) · [IEEE ICASSP 2025](https://ieeexplore.ieee.org/document/10888212)**

---

## The Core Idea {: .no_toc}

Every sampling theorem you've ever used, Shannon-Nyquist, compressed sensing, Prony's method, relies on a quiet assumption: you know the correct order of measurements.

But what if it isn't?

Think of a neuroscience experiment where you're imaging the activity of hundreds of neurons in a living organism. The animal moves. The microscope tracking isn't perfect, and the fluorescence trace you're recording for some "neuron 7" might actually contain samples from neuron 12, or neuron 3, or some mixture that changes at every time point. The data is all there, it's just been *shuffled across channels*.

This is not a noise problem. The measurements themselves are fine. The problem is that the *labels* are wrong, and you don't know which ones.

This project asks: **under what conditions can you reconstruct the original signals anyway?**

---

## 1. Formalizing the Shuffle

### 1.1 The Setup

Consider $M$ channels, each carrying a signal $\mathbf{x}_m \in \mathbb{C}^N$. At each time point $n$, the samples across all $M$ channels get permuted by some unknown permutation. Different time points can have different permutations. What you observe is a set of signals $\mathbf{y}_1, \dots, \mathbf{y}_M$ where each row of the matrix $\mathbf{Y} = (\mathbf{y}_1 \cdots \mathbf{y}_M)$ is a permutation of the corresponding row of $\mathbf{X} = (\mathbf{x}_1 \cdots \mathbf{x}_M)$.

In matrix form, this becomes:

$$
\begin{pmatrix} \mathbf{y}_1 \\ \vdots \\ \mathbf{y}_M \end{pmatrix}
= \mathbf{\Pi}
\begin{pmatrix} \mathbf{x}_1 \\ \vdots \\ \mathbf{x}_M \end{pmatrix}
$$

where $\mathbf{\Pi}$ is a structured permutation matrix built from binary diagonal blocks $ \mathbf{Q}_{mn}=\mathrm{diag}(\mathbf{q}_{mn}) $, with the constraint that each sample goes to exactly one channel: $\sum_m \mathbf{q}_{mn} = \sum_n \mathbf{q}_{mn} = \mathbf{1}$.

### 1.2 Why This Is Hard

At first glance, this looks hopeless. You have $N$ unknown permutations (one per time point), each choosing from $M!$ possibilities, plus the signal coefficients themselves. That's a combinatorial explosion on top of a continuous estimation problem.

The key insight is that **structure in the signals constrains the permutations**. If the signals live in a known low-dimensional subspace, say each $\mathbf{x}_m = \mathbf{E}\boldsymbol{\beta}_m$ for some $N \times K$ sensing matrix $\mathbf{E}$ with $K \ll N$, then not every permutation is consistent with the observed data. The subspace acts as a fingerprint: even after shuffling, the mathematical structure of the signals leaks through.

### 1.3 The Two-Channel Case: Building Intuition

To see why this works, start with just two channels. Here the permutation at each time point is binary: either the samples stay in place, or they swap. A single binary vector $\mathbf{q} \in \{0,1\}^N$ encodes the entire permutation: $q_n = 1$ means sample $n$ stays, $q_n = 0$ means it swaps.

If both $\mathbf{x}_1, \mathbf{x}_2 \in \mathscr{E}$ (the column space of $\mathbf{E}$), then asking "could there be a different pair of signals that produces the same shuffled output?" amounts to asking whether a certain intersection of subspaces is trivial. Specifically, if $r$ samples are *not* shuffled (i.e., $r$ entries of $\mathbf{q}$ are 1), those samples give us $r$ equations that constrain $\mathbf{x}_1' - \mathbf{x}_1''$ to lie in $\mathscr{E} \cap \ker(\rho)$, where $\rho$ is a coordinate projection. If $\mathbf{E}$ has the property that any $K$ rows are linearly independent (the **Restricted Full Rank Property**, or RFRP), then this intersection is $\{0\}$ whenever $r \geq K$, meaning the signals are uniquely determined.

The requirement is clean: **$N \geq 2K$ samples suffice for unique recovery in the two-channel case**, provided the sensing matrix satisfies the RFRP.

---

## 2. The General Theory

### 2.1 The Main Result: Shared Subspace

The two-channel argument generalizes to $M$ channels via a pigeonhole-style argument. If $N \geq MK$, then for any row of the permutation matrix, at least one diagonal block $\mathbf{Q}_{mn}$ must preserve at least $K$ coordinates. That's enough to pin down the signal using the RFRP, exactly as in the two-channel case.

**Theorem (Cross-Channel Unlabeled Sensing — Shared Subspace).**
*If all channel signals lie in the column space of an $N \times K$ matrix $\mathbf{E}$ satisfying the RFRP, the signals are all distinct, and $N \geq MK$, then the signals $\mathbf{x}_1, \dots, \mathbf{x}_M$ can be uniquely recovered from the shuffled observations $\mathbf{y}_1, \dots, \mathbf{y}_M$, up to a relabeling of the channels.*

The "up to relabeling" caveat is unavoidable: if you can swap all samples of two channels consistently, that's an equivalent solution. But no partial shuffles can create ambiguity.

### 2.2 Extension to a Union of Subspaces

In the shared-subspace setting, all channels use the same basis $\mathbf{E}$. But in many applications, different channels have different structures. A neuron firing in bursts lives in a different subspace than one with slow oscillations.

The ICASSP 2025 paper generalizes the framework to allow each channel to live in its own subspace $\mathscr{E}_m$. The condition becomes: every *pairwise sum* of subspaces $\mathscr{E}_m + \mathscr{E}_n$ must satisfy the RFRP, and $N \geq M \cdot \max_{m,n} \dim(\mathscr{E}_m + \mathscr{E}_n)$.

This is a strictly tighter bound than what you'd get by embedding everything in a single large subspace. It matters in practice because individual channel subspaces can be much smaller than their union.

### 2.3 Compressed Sensing as a Special Case

A particularly nice consequence: if signals are sparse in an overcomplete dictionary $\mathbf{D} \in \mathbb{C}^{N \times p}$ (with $p > N$), then each channel's subspace is spanned by whichever columns of $\mathbf{D}$ its coefficients are supported on. The union-of-subspaces theorem applies directly, and the RFRP condition reduces to requiring that $\mathbf{D}$ satisfies a $K \times K$-RFRP (any $K \times K$ submatrix has full rank), which random dictionaries satisfy with probability 1.

This means **shuffled compressed sensing**, recovering sparse signals from shuffled measurements, fits naturally into the framework.

---

## 3. From Continuous-Time Sparse Signals to Shuffled Recovery

### 3.1 Streams of Diracs

Many real-world signals are sparse not in a discrete dictionary, but in continuous time: neural spike trains, firefly flashes, radar pulses. These are modeled as streams of weighted Diracs:

$$x(t) = \sum_k a_k \, \delta(t - t_k)$$

where $t_k \in [0,1)$ are the spike locations and $a_k$ are their amplitudes. The Fourier series coefficients of such signals are sums of complex exponentials:

$$X_\ell = \sum_k a_k \, e^{-j2\pi t_k \ell}$$

In the frequency domain, each channel signal $\mathbf{x}_m$ lives in the column space of a structured matrix $\mathbf{E}_m = \mathbf{W}\mathbf{V}_m$, where $\mathbf{W}$ is the inverse DFT matrix and $\mathbf{V}_m$ is a Vandermonde matrix determined by the spike locations.

### 3.2 The Clever Trick: Permutation-Invariant Sums

Here's the key algorithmic insight: **adding the shuffled signals undoes the shuffle**.

Since the permutation at each time point just rearranges samples across channels, the sum $\mathbf{y}_1 + \cdots + \mathbf{y}_M = \mathbf{x}_1 + \cdots + \mathbf{x}_M$ is invariant to the permutation. This sum is itself a stream of Diracs (with all spike locations from all channels pooled together), and its parameters can be recovered via standard line spectral estimation (e.g., Prony's method).

From the recovered locations, you construct the sensing matrix $\mathbf{E} = \mathbf{W}\mathbf{V}_\Sigma$, and now the problem reduces to cross-channel unlabeled sensing with a known $\mathbf{E}$.

### 3.3 Why the Theory Works for This Structure

The final piece: does the matrix $\mathbf{E} = \mathbf{W}\mathbf{V}$ actually satisfy the RFRP?

Yes, and the proof is elegant. The key observation is that the product of an IDFT matrix and a Vandermonde matrix can be factorized as $\mathbf{W}_\mathcal{L}\mathbf{V} = \mathbf{D}_1 \mathbf{C} \mathbf{D}_2$, where $\mathbf{D}_1, \mathbf{D}_2$ are diagonal matrices and $\mathbf{C}$ is a **Cauchy matrix**. Cauchy matrices have a closed-form determinant expression, and under mild conditions on the Vandermonde nodes ($v_k^N \neq \pm 1$, which rules out only a measure-zero set), this determinant is guaranteed to be nonzero. Every $K \times K$ submatrix is invertible, so the RFRP holds.

**Theorem (Shuffled Multi-Channel Sparse Signal Recovery).**
*Given $M$ distinct sparse signals with a total of $K$ spike locations, if $N \geq MK$ and the spike locations satisfy mild genericity conditions, both the locations and amplitudes of all channels can be uniquely recovered from the shuffled observations, up to a relabeling of channels.*

---

## 4. A Practical Algorithm

The theory guarantees uniqueness, but doesn't hand you an algorithm. Here's the two-step approach from the Signal Processing paper:

**Step 1: Recover the support.** Sum the shuffled signals to get $\mathbf{y}_\Sigma = \mathbf{y}_1 + \mathbf{y}_2$, which equals $\mathbf{x}_1 + \mathbf{x}_2$ regardless of the shuffling. Apply denoising (structured low-rank approximation via ADMM) followed by Prony's method to estimate the spike locations. This gives the sensing matrix $\hat{\mathbf{E}}$.

**Step 2: Shuffled regression.** With the sensing matrix in hand, alternate between:
  - Estimating the coefficients $\boldsymbol{\beta}$ using a **robust MM-estimator** (because any wrong sample assignments act as outliers)
  - Updating the assignment vector $\mathbf{q}$ by solving a relaxed convex program, then projecting back to binary

The robust estimator is essential here. Standard least squares would break down because incorrectly assigned samples create arbitrary outliers in the residuals. The MM-estimator has a high breakdown point, it can tolerate a substantial fraction of contaminated data and still produce reasonable estimates.

The algorithm doesn't have convergence guarantees (the combination of robust estimation and binary projection makes the landscape nonconvex), but in practice 5 iterations are sufficient, selecting the solution with the smallest residual.

---

## 5. Validation: Whole-Brain Calcium Imaging

We tested the framework on real neural data from *Drosophila* (fruit fly) whole-brain calcium imaging. In this setting, a microscope tracks fluorescence changes in hundreds of neurons as the animal moves. Movement causes mismatches between recorded traces and their true source neurons, exactly the shuffling our theory addresses.

**Setup:**
- Baseline-corrected fluorescence traces from real *Drosophila* neurons
- A dictionary learned from unshuffled data via convolutional sparse coding (single kernel, circulant structure)
- The dictionary's RFRP was numerically verified by checking $100{,}000$ randomly sampled submatrices
- Shuffling simulated by randomly swapping pairs of 121-sample excerpts ($\approx 24$ seconds) at varying rates

**Results (1,000 Monte Carlo runs):**
- Up to **30% shuffled samples**: reconstruction quality (measured by $R^2$) remains close to the least-squares fit on unshuffled data
- At **35% shuffling**: still achieves $R^2 \approx 0.92$ and weighted assignment accuracy of 0.94
- Above **40%**: performance degrades sharply, but reassignment still provides significant gains over ignoring the shuffle entirely
- Even at **50% shuffling**, the robust reassignment substantially outperforms the naive MM-estimator

The sharp transition around 30–40% is characteristic of combinatorial problems: below a critical threshold, enough structure survives to guide the algorithm; above it, the problem becomes fundamentally ambiguous.

---

## 6. What This Connects To

**Unlabeled sensing** is the broader field, recovering signals when the correspondence between measurements and their indices is unknown. Most existing work assumes i.i.d. Gaussian sensing matrices. Our framework handles the structured matrices that actually arise in signal processing (DFT, Vandermonde), which is what makes the connection to continuous-time sparse signals possible.

**Compressed sensing** emerges as a special case: when signals are sparse in an overcomplete dictionary, the union-of-subspaces theorem applies directly, and you get recovery guarantees for "shuffled compressed sensing" essentially for free.

**Multi-target tracking** is another natural application. When trajectories cross, data association becomes ambiguous, you lose track of which observation belongs to which target. The framework provides conditions under which the correct associations can be recovered from the signal structure alone.

---

## Publications

- **T. Koka**, M. C. Tsakiris, M. Muma, B. Béjar Haro. *Shuffled multi-channel sparse signal recovery.* **Signal Processing** (Elsevier), 2024. [DOI](https://doi.org/10.1016/j.sigpro.2024.109524)
- **T. Koka**, M. C. Tsakiris, B. Béjar Haro, M. Muma. *Cross-Channel Unlabeled Sensing over a Union of Signal Subspaces.* **IEEE ICASSP**, 2025.