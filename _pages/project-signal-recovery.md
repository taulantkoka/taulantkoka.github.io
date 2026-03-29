---
title: "Shuffled Multi-Channel Signal Recovery"
permalink: /projects/signal-recovery/
layout: single
sidebar:
  nav: "projects"
---

`Python` · `MATLAB` · `Optimization` · `Sparse Recovery`

## The Problem

Imagine you're recording signals from multiple channels — say, neural activity from different neurons — but somewhere along the way, the labels got mixed up. You don't know which measurement came from which channel. Can you still figure out the original signals?

That's the core question behind this project. It's a surprisingly tricky problem because it combines two hard things: sparse signal recovery and a combinatorial permutation puzzle.

## What I Did

I formulated this as sparse recovery over a union of signal subspaces and developed optimization-based algorithms that jointly figure out both the signal content and the correct channel assignments. The idea is that you can exploit the structure of the signals themselves to untangle the mess.

This started during my Master's thesis at EPFL, where I worked on multi-input signals with unknown channel membership, and continued into my PhD.

## Where It Applies

- **Calcium imaging** — resolving overlapping signals from different neurons
- **Multi-target tracking** — matching observations to the right targets when you've lost track of who's who

## Papers

- **T. Koka**, M. C. Tsakiris, B. Béjar Haro, M. Muma. *Cross-Channel Unlabeled Sensing over a Union of Signal Subspaces.* **IEEE ICASSP**, 2025.
- **T. Koka**, M. C. Tsakiris, M. Muma, B. Béjar Haro. *Shuffled multi-channel sparse signal recovery.* **Signal Processing**, 2024.
