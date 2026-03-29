---
title: "Computer Vision Pipeline at PSI"
permalink: /projects/psi-cv-pipeline/
layout: single
sidebar:
  nav: "projects"
---

**Tags:** `Python` · `Computer Vision` · `Signal Processing` · `X-ray Crystallography`

## Overview

During a four-month internship at the [Swiss Data Science Center (SDSC)](https://datascience.ch) hub at the [Paul Scherrer Institute (PSI)](https://www.psi.ch/en), I built an end-to-end computer vision pipeline for background estimation and signal (spot) detection in noisy X-ray crystallography imaging data.

## Approach

- Combined signal processing, denoising, and statistical modeling techniques to handle the high noise and artifact levels typical in crystallographic images.
- Designed robust detection algorithms under artifact-heavy conditions, improving the reliability of automated downstream analysis.
- The pipeline covers the full workflow from raw image ingestion through background subtraction to spot detection and evaluation.

## Context

X-ray crystallography produces diffraction images where individual spots correspond to structural information about the sample. Accurately detecting these spots against a noisy, non-uniform background is essential for downstream structure determination. This pipeline automates that process.

## Key Tools

Python, NumPy, SciPy, OpenCV, scikit-image

<!-- [View on GitHub](https://github.com/taulantkoka/your-repo){: .btn .btn--primary .btn--small} -->
