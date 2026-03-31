---
title: "About"
permalink: /about/
layout: single
mathjax: true
---

## Hey, I'm Taulant

I'm a Ph.D. candidate at [TU Darmstadt](https://www.etit.tu-darmstadt.de/rds/index.en.jsp). My work is broadly about making sense of noisy data, whether that's from a city-wide monitoring network, a biological sensor, or a genomic sequence. I care a lot about methods that are statistically honest and computationally realistic at the same time.

### The Path Here

I grew up in Germany, did my B.Sc. and M.Sc. at TU Darmstadt in Electrical Engineering and Information Technology, and took detours to [Aalto University](https://www.aalto.fi/en) in Finland and [EPFL](https://www.epfl.ch/en/) in Switzerland along the way. During my Master's, I also spent two years as a working student in electronics hardware development at Thomas Magnete GmbH. At EPFL I joined the [LCAV lab](https://www.epfl.ch/labs/lcav/) and wrote my thesis in collaboration with the [Swiss Data Science Center (SDSC)](https://datascience.ch). After that I spent a few months as an intern at the SDSC Hub at the [Paul Scherrer Institute](https://www.psi.ch/en), before I returned to Darmstadt to start my PhD in late 2022.

### Recent Research

**High-dimensional variable selection.** When you're dealing with millions of features, false discoveries are almost guaranteed unless you're careful. A large part of my current research is about building variable selection methods that scale to these regimes while keeping the false discovery rate under control. The tricky part isn't just the statistics — it's that naive implementations need terabytes of RAM, so the algorithmic design has to be memory-aware from the ground up.

**Cross-channel unlabeled sensing.** Classical sampling theory assumes you know which measurement came from which channel. What happens when that assumption breaks? During my Master's thesis, we formalized a problem called Cross-Channel Unlabeled Sensing, where multi-channel signals are observed through unknown permutations at each time point. My recent work derives recovery bounds and tackles the combinatorial structure underneath. This shows up in practice in settings like whole-brain calcium imaging, where movement can scramble which recorded trace belongs to which neuron, or generally in multi-target tracking, where trajectories cross and identities get mixed up.

### Projects & Interests

Beyond my day job as a research associate, I like tinkering with public data and building things:

- **[Air quality in Germany](/projects/airquality/).**  I'm analyzing 25 years of public monitoring data to figure out whether diesel bans and Umweltzonen actually reduced pollution, or whether fleet modernization would have done the job anyway. What started as an afternoon project turned into a proper study covering 78 cities.
- **ECG analysis.**  During my Master's, I built a visibility-graph-based method for R-peak detection in noisy ECG signals. It got integrated into [NeuroKit2](https://github.com/neuropsychology/NeuroKit).
- **Hardware.**  I have a background in measurement and sensor technology and occasionally tinker with microcontroller and Arduino projects.

---

[GitHub](https://github.com/taulantkoka) · [LinkedIn](https://linkedin.com/in/taulant-koka-36a7971b6) · [Google Scholar](https://scholar.google.com/citations?user=dbpEvYUAAAAJ&hl=de) · [Contact](/contact/)