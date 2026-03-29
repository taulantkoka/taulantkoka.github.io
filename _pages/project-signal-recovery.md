---
title: "Shuffled Multi-Channel Signal Recovery"
permalink: /projects/signal-recovery/
layout: single
sidebar:
  nav: "projects"
---

**Tags:** `Python` · `Optimization` · `Sparse Recovery` · `Union of Subspaces`

## Overview

This project develops optimization-based algorithms for recovering multi-channel signals from permutation-corrupted measurements — a challenging combinatorial-continuous optimization problem that arises in applications such as calcium imaging and multi-target tracking.

## Approach

- Formulated the problem as sparse recovery over a union of signal subspaces.
- Designed algorithms that jointly recover the signal content and resolve the unknown permutation (channel assignment) of measurements.
- The framework extends classical sparse recovery to settings where the association between measurements and channels is unknown ("unlabeled sensing").

## Applications

- **Calcium imaging** — resolving overlapping neural signals
- **Multi-target tracking** — associating observations with targets under unknown correspondence
- **Multi-input FRI signals** — reconstruction from samples with unknown channel membership (Master's thesis topic at EPFL/LCAV)

## Publications

- **T. Koka**, M. C. Tsakiris, B. Béjar Haro, M. Muma. *Cross-Channel Unlabeled Sensing over a Union of Signal Subspaces.* **IEEE ICASSP**, 2025.
- **T. Koka**, M. C. Tsakiris, M. Muma, B. Béjar Haro. *Shuffled multi-channel sparse signal recovery.* **Signal Processing**, 2024.

## Key Tools

Python, MATLAB, NumPy, SciPy, optimization solvers

<!-- [View on GitHub](https://github.com/taulantkoka/your-repo){: .btn .btn--primary .btn--small} -->
