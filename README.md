# dSUNO / RB-ICD for Scan-Adaptive Dynamic MRI Sampling

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.8+](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/downloads/)
[![arXiv](https://img.shields.io/badge/arXiv-2602.13984-b31b1b.svg)](https://arxiv.org/abs/2602.13984)

Official Python implementation accompanying the paper:

**Scan-Adaptive Dynamic MRI Undersampling Using a Dictionary of Efficiently Learned Patterns**  
**Siddhant Gautam**, Angqi Li, Prachi P. Agarwal, Anil K. Attili, Jeffrey A. Fessler, Nicole Seiberlich, Saiprasad Ravishankar  
*Under review at Magnetic Resonance in Medicine (MRM); available as an arXiv preprint.*

📄 **arXiv:** https://arxiv.org/abs/2602.13984

---

## Overview

Dynamic cardiac cine MRI is often limited by long acquisition times. This repository implements a **scan- and slice-adaptive Cartesian undersampling** framework for dynamic MRI that:

1. **Learns a dictionary of optimized sampling masks** from fully sampled training cine time-series.
2. **Selects a scan-adaptive mask at test time** using a nearest-neighbor search in low-frequency k-space, then applies it across the entire dynamic series.
3. Reconstructs the undersampled series using **MostNet**, an unrolled optimization network that combines a learned spatiotemporal prior with **conjugate-gradient data consistency**.

---

## Method Summary

### 1) MostNet: Model-Based Spatiotemporal Network (Reconstruction)

MostNet reconstructs a dynamic image series $x \in \mathbb{C}^{n \times N_t}$ from undersampled multi-coil measurements using the forward model:

<p align="center">

$$
y_i = M F S_i x
$$

</p>

where $M$ is the sampling mask, $F$ is the 2D spatial Fourier transform (applied per frame), and $S_i$ is the coil sensitivity operator.

MostNet solves a MoDL-style objective with a learned spatiotemporal prior:

<p align="center">

$$ 
\arg\min_x \sum_{i=1}^{n_c} ||M F S_i x - y_i\|_2^2 + \lambda \|x - \tilde{{D}}_\theta(x)||_2^2
$$

</p>

where the denoiser $\tilde{\mathcal{D}}_\theta$ is a **dual-domain CRNN** (x–t and x–f) combination:

<p align="center">

$$
\tilde{\mathcal{D}}_\theta(x)
=
\gamma\, D_{xt}(x)
+
(1-\gamma)\,
F_t^{-1}\!\left(D_{xf}(F_t x)\right)
$$

</p>

**Unrolling (K stages):** at stage $k$, apply denoising followed by a CG-based data-consistency update:

- $z^k = \tilde{\mathcal{D}}_\theta(x^k)$
- $x^{k+1} =
\arg\min_x
\sum_{i=1}^{n_c}
\|M F S_i x - y_i\|_2^2
+ \lambda \|x - z^k\|_2^2$

(solved with a fixed number of conjugate-gradient iterations)

---

### 2) Joint Optimization (Mask Dictionary + Reconstruction)

We jointly learn:

- a set of scan- and slice-adaptive masks $\{M_i\}$ (a dictionary of optimized patterns),
- a reconstruction network $f_\theta$ trained across those learned patterns,

via **alternating optimization**: update masks for fixed reconstruction, then update $\theta$ for fixed masks.

---

### 3) RB-ICD: Randomized Subset-Based ICD (Mask Optimization)

Updating one phase-encoding line at a time is expensive for dynamic cine data because each candidate evaluation requires reconstructing an entire multi-frame series. RB-ICD accelerates this by **updating multiple phase-encoding locations simultaneously** (subset updates).

**Algorithm sketch (Algorithm 1 in the paper):**

- Start from an initial mask $M_{\text{init}}$ and enforce a fixed low-frequency ACS set $\Omega_{\text{low}}$.
- For each pass $j = 1, \ldots, N_{\text{iter}}$:
  - Randomly partition movable sampled locations into disjoint subsets of size $s$.
  - For each subset:
    - Sample $N_{\text{cand}}$ candidate relocations to unsampled locations,
    - Reconstruct and evaluate the loss for each candidate,
    - Accept the best candidate if it improves the current loss.

---

## Citation

If you use this code, please cite:

```bibtex
@article{gautam_dsuno_2026,
  title   = {Scan-Adaptive Dynamic MRI Undersampling Using a Dictionary of Efficiently Learned Patterns},
  author  = {Gautam, Siddhant and Li, Angqi and Agarwal, Prachi P. and Attili, Anil K. and Fessler, Jeffrey A. and Seiberlich, Nicole and Ravishankar, Saiprasad},
  journal = {Magnetic Resonance in Medicine},
  note    = {under review},
  year    = {2026},
  url     = {https://arxiv.org/abs/2602.13984}
}
```

**Contact**  
The code is provided to support reproducible research. If you have any questions about the code or have some trouble running any module of the framework, you can contact Siddhant Gautam (gautamsi@msu.edu) or Saiprasad Ravishankar (ravisha3@msu.edu).
