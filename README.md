# Dynamic Ptychography Ring Segmentation with SOM–CNN

Unsupervised, physics-aware segmentation of dynamic ptychographic
diffraction patterns using Self-Organizing Maps (SOM) and a lightweight
CNN/U-Net student.

This demo illustrates how SOM can discover and track ring-like scattering
patterns (e.g., from gold nanoparticles) over time, and how a CNN student
can inherit these segmentation rules without any manual labels.

See the full slide deck in
[`docs/ptychography_dynamic_gold_demo.pdf`](docs/ptychography_dynamic_gold_demo.pdf).

---

## Problem

- Time-resolved ptychography experiments generate thousands of
  diffraction frames.
- Manual segmentation of rings and scattering features is infeasible.
- Goal: **segment and track ring-like patterns and background regions
  over time in a fully label-free way**, with interpretable clustering.

---

## Data

- Dynamic ptychographic diffraction images across multiple time frames
  (e.g., 0–20 ms).
- Each frame contains:
  - background,
  - ring-like scattering from nanoparticles or evolving structures,
  - possible core/intense spots.

No pixel-level ground-truth masks are assumed.

---

## Method (SOM → CNN)

1. **Physics-aware feature extraction**

   For each pixel in a diffraction frame, a low-dimensional feature
   vector is computed, capturing:

   - intensity in log space,
   - local contrast and gradient magnitude,
   - radial position / distance from beam center,
   - neighborhood statistics.

2. **PCA + SOM clustering**

   - PCA reduces the feature dimension.
   - A Self-Organizing Map (SOM) is trained on a subset of frames to
     form a small number of clusters (e.g., background, ring, core).
   - Cluster centroids (weight vectors) are interpreted in terms of
     intensity, radial location, and sharpness.

3. **Dynamic reuse across frames**

   - The same SOM centroids are **reused across time frames**:
     each new frame is projected onto the learned codebook.
   - This yields consistent labels for background vs ring vs core
     over time, enabling dynamic analysis of scattering evolution.

4. **CNN/U-Net student (optional)**

   - A lightweight CNN/U-Net is trained with SOM labels as supervision.
   - The student model provides smooth, pixel-level segmentations with
     fast inference for large batches of frames.

Key properties:

- **Label-free:** no hand-drawn ring masks are required.
- **Interpretable:** SOM clusters correspond to specific intensity and
  radial regimes (background, ring, core).
- **Temporal consistency:** the same codebook is applied to all frames,
  enabling quantitative tracking of ring area, intensity, and motion.

---

## Results (see PDF)

Highlights from `docs/ptychography_dynamic_gold_demo.pdf`:

- SOM clustering cleanly separates the diffraction pattern into
  background, ring, and (optionally) central core.
- The fractional area / intensity of the ring cluster can be tracked
  over time, providing a simple descriptor of dynamic behavior.
- CNN student models reproduce SOM segmentations at pixel level,
  enabling rapid processing of long time series.

---

## Status

This repository currently serves as a **documentation/demo hub** for the
dynamic ptychography SOM–CNN pipeline. Code and runnable notebooks will
be added progressively as the framework is cleaned and released.
# ptychography-som-cnn-demo
