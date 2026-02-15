# dSUNO / RB-ICD: Scan-Adaptive Dynamic MRI Undersampling

[![arXiv](https://img.shields.io/badge/arXiv-XXXX.XXXXX-b31b1b.svg)](https://arxiv.org/abs/XXXX.XXXXX)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE.md)
[![Python 3.8+](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/downloads/)

Official Python implementation accompanying the paper:

**Siddhant Gautam**, Angqi Li, Prachi P. Agarwal, Anil K. Attili, Jeffrey A. Fessler, Nicole Seiberlich, Saiprasad Ravishankar  

*Preprint (arXiv), 2026*

📄 **arXiv:** https://arxiv.org/abs/XXXX.XXXXX  

---

## Overview

This work proposes a scan-adaptive framework for accelerating dynamic cardiac MRI using a dictionary of efficiently learned Cartesian undersampling patterns.

Given fully sampled training dynamic MRI data, we learn a dictionary of optimized sampling masks using a randomized batched iterative coordinate descent (RB-ICD) algorithm.  
At test time, a nearest-neighbor search in low-frequency $k$-space selects an appropriate sampling pattern for each scan. The framework enables improved reconstruction quality while maintaining practical computational efficiency.

---

## RB-ICD Sampling Optimization Framework

The RB-ICD algorithm performs subset-wise optimization of Cartesian sampling masks by iteratively evaluating candidate updates guided by a reconstruction network.

<p align="center">
  <img src="figures/rbicd_pipeline.png" alt="RB-ICD pipeline" />
</p>

---

## Scan-Adaptive Mask Selection (Nearest Neighbor Search)

At inference time, we select a scan-adaptive mask by comparing low-frequency reconstructions between test and training scans.

<p align="center">
  <img src="figures/nearest_neighbor_pipeline.png" alt="Nearest neighbor search" />
</p>

---

## Repository Structure

The codebase is organized into the following main components:

- RB-ICD mask optimization
- Dictionary-based nearest-neighbor search
- Dynamic MRI reconstruction pipelines (MostNet, MoDL-based variants)
- Scripts to reproduce figures and quantitative evaluation results

---

## Planned Release

🚧 The complete codebase will be released upon acceptance of the manuscript at *Magnetic Resonance in Medicine (MRM)*, in accordance with journal reproducibility guidelines.

---

## Datasets

Experiments were performed using:

- Public OCMR dynamic cardiac MRI dataset  
- In-house cardiac MRI dataset (University of Michigan)

---

## Citation

If you use this code or framework in your research, please cite:

```bibtex
@article{gautam2026dsuno,
  title={Scan-Adaptive Dynamic MRI Undersampling Using a Dictionary of Efficiently Learned Patterns},
  author={Gautam, Siddhant and Li, Angqi and Agarwal, Prachi P. and Attili, Anil K. and Fessler, Jeffrey A. and Seiberlich, Nicole and Ravishankar, Saiprasad},
  journal={arXiv preprint},
  year={2026}
}
