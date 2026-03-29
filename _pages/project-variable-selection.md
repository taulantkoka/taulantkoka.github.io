---
title: "Scalable Variable Selection with Statistical Error Control"
permalink: /projects/variable-selection/
layout: single
sidebar:
  nav: "projects"
---

**Tags:** `Python` · `C++` · `pybind11` · `High-Dimensional Statistics` · `FDR Control`

## Overview

This project introduces a novel framework for high-dimensional variable selection with false discovery rate (FDR) control. It addresses the challenge of identifying truly relevant variables among potentially millions of candidates while providing statistical guarantees on the proportion of false discoveries.

## Approach

- Developed a method based on sequential sampling of null features ("virtual dummies") that enables scalable FDR-controlled selection.
- Implemented a memory-aware C++ core with Python bindings via pybind11, achieving orders-of-magnitude reduction in memory usage compared to existing approaches.
- Reduced resource requirements from terabyte-scale to hundreds of MB through algorithmic optimization, enabling deployment on standard infrastructure.

## Validation

- Validated on datasets with 500k+ samples and millions of features.
- Conducted large-scale experiments and parameter sweeps on HPC clusters (Slurm) with systematic benchmarking, logging, and reproducible workflows.

## Publication

**T. Koka**, J. Machkour, D. P. Palomar, M. Muma. *Virtual Dummies: Enabling Scalable FDR-Controlled Variable Selection via Sequential Sampling of Null Features.* (Manuscript / submission draft)

## Key Tools

Python, C++, pybind11, NumPy, SciPy, Eigen, OpenMP, BLAS/CBLAS, Slurm HPC

<!-- [View on GitHub](https://github.com/taulantkoka/your-repo){: .btn .btn--primary .btn--small} -->
