# Stipple: Real-Time Incremental Gaussian Splatting with Visual-Inertial Tracking

**Kilian Northoff, Mateo de Mayo, Daniel Cremers**\
Technical University of Munich - Munich Center for Machine Learning

Accepted to the **ECCV NeuSLAM Workshop, Nectar Track**.

- 📄 Paper: https://arxiv.org/abs/2508.00088
- 🌐 Project page: https://<your-github-username>.github.io/tumvision-stipple/ (served from [`docs/`](docs/))
- 💻 Code: _coming soon_

---

## Abstract

3D Gaussian Splatting (3DGS) provides efficient rendering of photo-realistic scenes,
but its heavy preprocessing and training steps make it a poor fit for applications
that require real-time reconstruction in robotics or XR. This capability is important
since it allows immediate feedback and interaction with new environments.
Visual-inertial odometry (VIO) and simultaneous localization and mapping (VI-SLAM)
systems, on the other hand, specifically target real-time applications, which makes
them a good choice for integration with 3DGS. We propose a new method that tracks
and reconstructs simultaneously in real-time by leveraging an efficient
visual-inertial tracking system based on Basalt together with a novel incremental
method built on top of Brush, an efficient Rust-based GPU-vendor-agnostic
implementation of 3D Gaussian Splatting. We show that many of the heavy
preprocessing and training steps of 3DGS can be replaced with a more efficient
incremental training strategy that has direct access to the information generated
by the visual-inertial tracking system. Furthermore, we propose and combine
multiple practical improvements to increase the efficiency of the training pipeline
and adapt it to run in real-time, parallel to the tracking thread. This work
highlights the value of exploiting the complementary nature of SLAM and 3DGS, and
how that can lead to promising results for real-time 3D reconstruction.

## Highlights

- **One real-time pipeline.** Stereo images + IMU in, camera poses and a live 3DGS map
  out, tracking and splatting run concurrently, and the SLAM thread never blocks on training.
- **VIO-aware training.** Gaussians are only ever seeded from *anchor frames* (~10% of
  keyframes with unreconstructed content), which confines the stereo-depth bottleneck
  (FoundationStereo, ~150 ms) to a small fraction of frames.
- **Occupancy-pruned, SSIM-guided densification.** New Gaussians are back-projected from
  metric depth, pruned against a 0.1 m occupancy grid, then densified where per-pixel SSIM
  says the render still disagrees with the image.
- **Loop closure for free.** Pose-graph corrections from the tracker are applied as rigid
  transforms to each anchor frame's contiguous block of Gaussians.
- **GPU-vendor-agnostic.** Built on Brush (Rust + CubeCL, no CUDA lock-in), with BM and
  SGBM-WLS stereo fallbacks so the pipeline can run on non-CUDA GPUs.

## Results at a glance

Evaluated on **EuRoC** and the **Monado SLAM Dataset (MSD)**, best of 3 runs at equal
wall-clock time, on a Ryzen 9 9950X + RTX 5060 Ti.

- **EuRoC Vicon Room:** 23.5 dB avg PSNR vs. 20.2 (Photo-SLAM) / 22.4 (CaRtGS), at
  ~211 tracking FPS vs. ~70.
- **MSD Valve Index / HP Reverb G2 / Samsung Odyssey+:** best PSNR, LPIPS and tracking FPS
  on average, and the only method to complete several sequences where the baselines fail.
- **EuRoC Machine Hall:** the one regression, an unresolved Gaussian-explosion pathology
  over-triggers densification and CaRtGS leads on PSNR/SSIM there.

## Videos

- EuRoC Vicon Room: https://www.youtube.com/watch?v=M5Nhw3y3AqA
- DepthAI live demo: https://www.youtube.com/watch?v=bbBo62U363o
- XREAL live demo: https://www.youtube.com/watch?v=H4PsfCmxbo8

## Figures

![Figure 1. Qualitative comparison of Photo-SLAM, CaRtGS, our method, and ground truth on EuRoC and MSD sequences.](static/images/fig1_qualitative.jpg)

![Figure 2. System overview diagram.](static/images/fig2_system_overview.png)

![Figure 3. Keyframe lifecycle flowchart.](static/images/fig3_keyframe_lifecycle.png)

## Citation

```bibtex
@article{northoff2026stipple,
  title   = {Stipple: Real-Time Incremental Gaussian Splatting with Visual-Inertial Tracking},
  author  = {Northoff, Kilian and de Mayo, Mateo and Cremers, Daniel},
  journal = {arXiv preprint arXiv:2508.00088},
  year    = {2026}
}
```

## Acknowledgements

Supported by the European Research Council (ERC) Advanced Grant SIMULACRON, by the DFG
project CR 250/26-1 "4D-YouTube", by the GNI Project "AI4Twinning", and by the Munich
Center for Machine Learning.
