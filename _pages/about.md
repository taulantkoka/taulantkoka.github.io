---
title: "About"
permalink: /about/
layout: single
mathjax: true
---

## Hey, I'm Taulant

I’m a Ph.D. candidate at TU Darmstadt. My work is broadly about making sense of noisy data, whether that's from a city-wide monitoring network, a biological sensor, or a genomic sequence.

This page is meant to be a dynamic archive for a selection of problems and projects I’ve found particularly challenging, interesting, or both. I try to share them here in an accessible way, and give some additional information regarding thought processes that maybe didn't quite fit into a research paper.

### The Path Here

I grew up in Germany, did my B.Sc. and M.Sc. at TU Darmstadt in Electrical Engineering and Information Technology, and took detours to Aalto University in Finland and EPFL in Switzerland along the way. At EPFL I joined the LCAV lab and wrote my thesis in collaboration with the Swiss Data Science Center (SDSC). After that I spent a few months as an intern at the SDSC Hub at the Paul Scherrer Institute, before I returned to Darmstadt to start my PhD in late 2022.

### Recent Research Directions

**High-Dimensional Variable Selection**---When dealing with millions of features, it is easy to make false discoveries. Currently, a large portion of my research focuses on the development of scalable variable selection with False Discovery Rate (FDR) control to ensure statistical validity. Memory and hardware constraints in general, play a crucial role, when problems start to scale into regimes that require terabytes of RAM. Maintaining the balance between statistical rigor and computational efficiency is therefore indespensable.

**Cross-Channel Unlabeled Sensing**---While classical sampling assumes fixed indexing, **unlabeled sensing** addresses cases where samples are "out of order." **Cross-Channel Unlabeled Sensing**, a sub-problem of unlabeled sensing we formalized during my Master's Thesis, deals with reconstructing multi-channel signals when samples are permuted across channels at each time point. Moving beyond my initial thesis work, my recent research derives recovery bounds and addresses the underlying combinatorial challenges. This is particularly relevant for whole-brain calcium imaging or multi-target tracking in general, where data association can be difficult due to crossings of trajectories.


### Projects & Interests

Beyond my job as a research associate, I enjoy tinkering with public data and open-source tools:

* **Air Quality in Hessen**: I am currently [analyzing public data](/projects/) to quantify the impact of "Umweltzonen" (low-emission zones) on urban air quality over the last decade.
* **ECG Analysis**: As a side project during my Master's, I developed a visibility-graph-based method for R-peak detection in noisy ECG signals. It was integrated into [NeuroKit2](https://github.com/neuropsychology/NeuroKit), a library widely used in the neuroscience and biomedical engineering communities.
* **Intersection with Hardware**: Additionally, I have a background in measurement and sensor technology and occasionally work on microcontroller and Arduino-based projects in my free time.
  
---

[GitHub](https://github.com/taulantkoka) · [LinkedIn](https://linkedin.com/in/taulant-koka-36a7971b6) · [Google Scholar](https://scholar.google.com/citations?user=dbpEvYUAAAAJ&hl=de) · [Contact](/contact/)