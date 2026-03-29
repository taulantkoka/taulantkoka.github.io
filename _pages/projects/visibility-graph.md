---
title: "Visibility Graph R-Peak Detection"
permalink: /projects/visibility-graph/
layout: single
sidebar:
  nav: "projects"
---

**Tags:** `Python` · `Graph Algorithms` · `ECG` · `Biomedical Signal Processing`

## Overview

This project introduces a visibility-graph-based algorithm for fast and sample-accurate R-peak detection in noisy ECG signals. The method transforms the ECG time series into a graph representation and leverages its structural properties for robust detection, even under heavy noise conditions.

## Impact

The algorithm has been adopted into the [**neurokit2**](https://github.com/neuropsychology/NeuroKit) open-source library, making it available to the broader biomedical signal processing community.

## Approach

- Constructed visibility graphs from ECG time series, encoding local and global signal structure.
- Designed a detection rule based on graph properties that is both fast and sample-accurate.
- Follow-up work by Emrich et al. (EUSIPCO 2023) further accelerated the method.

## Publications

- **T. Koka**, M. Muma. *Fast and sample accurate R-peak detection for noisy ECG using visibility graphs.* **IEEE EMBC**, 2022.
- J. Emrich, **T. Koka**, S. Wirth, M. Muma. *Accelerated Sample-Accurate R-Peak Detectors Based on Visibility Graphs.* **EUSIPCO**, 2023.

## Key Tools

Python, NumPy, SciPy, networkx, neurokit2

<!-- [View on GitHub](https://github.com/taulantkoka/your-repo){: .btn .btn--primary .btn--small} -->
