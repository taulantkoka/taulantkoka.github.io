---
title: "About"
permalink: /about/
layout: single
mathjax: true
---

## Hey, I'm Taulant

I’m a Ph.D. candidate at [TU Darmstadt](https://www.etit.tu-darmstadt.de/rds/index.en.jsp), focusing on the intersection of machine learning and signal processing. My work centers on high-dimensional variable selection. Specifically, how can I identify meaningful patterns in massive datasets while ensuring those patterns aren't just statistical artifacts.

I’m drawn to problems where sheer computational power isn't enough. Most of my research involves developing algorithms that can handle sparse signals and messy measurements under strict memory and reliability constraints. In practice, this means balancing theoretical guarantees with high-performance implementation in C++ and Python.

### The Path Here

I grew up in Germany, did my B.Sc. and M.Sc. at TU Darmstadt in Electrical Engineering, and took detours to Aalto University in Finland and EPFL in Switzerland along the way. At EPFL I joined the LCAV lab and wrote my thesis in collaboration with the Swiss Data Science Center (SDSC). After that I spent a few months as an intern at the SDSC Hub at the Paul Scherrer Institute, before I returned to Darmstadt to start my PhD in late 2022.

### What I Work On

**High-Dimensional Variable Selection**---When dealing with millions of features, it is easy to make false discoveries. Currently, a large portion of my research focuses on the development of scalable variable selection with False Discovery Rate (FDR) control to ensure statistical validity. Memory and hardware constraints in general, play a crucial role, when problems start to scale into regimes that require terabytes of RAM. Maintaining the balance between statistical rigor and computational efficiency is therefore indespensable.

**Cross-Channel Unlabeled Sensing**---While classical sampling assumes fixed indexing, **unlabeled sensing** addresses cases where samples are "out of order." **Cross-Channel Unlabeled Sensing**, a sub-problem of unlabeled sensing we formalized during my Master's Thesis, deals with reconstructing multi-channel signals when samples are permuted across channels at each time point. Moving beyond my initial thesis work, my recent research derives recovery bounds and addresses the underlying combinatorial challenges. This is particularly relevant for whole-brain calcium imaging or multi-target tracking in general, where data association can be difficult due to crossings of trajectories.


### Projects & Interests

Beyond my thesis, I enjoy tinkering with public data and open-source tools:

* **Air Quality in Hessen**: I am currently [analyzing public data](/projects/) to quantify the impact of "Umweltzonen" (low-emission zones) on urban air quality over the last decade.
* **ECG Analysis**: As a side project during my Master's, I developed a visibility-graph-based method for R-peak detection in noisy ECG signals. It was integrated into [NeuroKit2](https://github.com/neuropsychology/NeuroKit), a library widely used in the neuroscience and biomedical engineering communities.
* **Intersection with Hardware**: I have a strong background in measurement and sensor technology and occasionally work on microcontroller and Arduino-based projects in my free time. The intersection of signal processing and statistics with the hardware domain proves time and time again to be a rewarding combination.
  
---

[GitHub](https://github.com/taulantkoka) · [LinkedIn](https://linkedin.com/in/taulant-koka-36a7971b6) · [Google Scholar](https://scholar.google.com/citations?user=dbpEvYUAAAAJ&hl=de) · [Contact](/contact/)