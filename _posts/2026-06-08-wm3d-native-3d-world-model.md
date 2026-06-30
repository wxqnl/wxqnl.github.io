---
title: "WM3D: Native-3D World Modeling for Robot Action"
date: 2026-06-08
permalink: /posts/2026/06/wm3d-native-3d-world-model/
excerpt: "WM3D argues that robot world models should predict explicit 3D state — depth, point geometry, pose, robot state, task text, and action-conditioned dynamics — then render video as a view of that imagined world."
tags:
  - world model
  - embodied AI
  - 3D
  - robot manipulation
---

Most world models predict the future in **pixel or video-latent space**. **WM3D** takes a different route: for robot manipulation, the state that matters is the **3D world** — depth, point geometry, camera/robot pose, task text, and the action that changes the scene.

> Can a robot world model keep native 3D as its core state space, while still producing action-aware, high-quality RGB rollouts?

That is the current WM3D story. Pixels are useful as observations and demos, but they are not the physical state a robot acts on. A plausible video can still hide the wrong depth, contact, or object motion. WM3D therefore trains action-conditioned dynamics directly over native-3D supervision, and treats RGB video as a rendered view of the imagined state.

![WM3D architecture comparison](/wm3d/assets/native_3d_wm_architecture_comparison.png)

## Why this is different

| Approach | Main state | Strength | Limitation for robotics |
| --- | --- | --- | --- |
| Video-latent world models | future frames / video tokens | strong visual prior and photorealism | action→geometry causality is implicit |
| Compact dynamics / policy models | low-dimensional control state | efficient and stable for policies | can lose rich visuospatial structure |
| **WM3D** | **native 3D + action-conditioned dynamics** | **explicit geometry, depth, pose, progress, and policy interfaces** | RGB must be rendered without taking over the state |

The latest architecture makes this boundary explicit. WM3D owns the world state and action reasoning. A stronger action-aware video branch renders RGB, but the native-3D core remains central: depth, point/geometry, pose, action, proposer, progress, and policy objectives are still trained as first-class targets.

## Benchmark snapshot

Open-loop future prediction on a balanced OXE/DROID protocol shows WM3D beating a last-frame baseline across the main RGB metrics:

| Metric | WM3D | Last-frame | Delta |
| --- | ---: | ---: | ---: |
| PSNR ↑ | **28.374** | 23.285 | **+21.9%** |
| SSIM ↑ | **0.9135** | 0.8744 | **+4.5%** |
| LPIPS ↓ | **0.0985** | 0.1199 | **−17.9%** |
| RGB L1 ↓ | **0.0199** | 0.0321 | **−38.1%** |
| Motion RGB L1 ↓ | **0.0648** | 0.1409 | **−54.0%** |
| R3D18-FVD ↓ | **22.037** | 28.157 | better |

The more important evidence is native-3D action sensitivity:

| Metric | Value | Reading |
| --- | ---: | --- |
| Depth future L1 ↓ | **0.02659** | future depth is directly supervised |
| Depth-change cosine ↑ | **0.6735** | captures how geometry changes |
| Depth-change sign accuracy ↑ | **0.8193** | predicts the direction of depth change |
| Real-action token win-rate ↑ | **0.8650** | real actions beat counterfactual alternatives |
| Real-action depth win-rate ↑ | **0.7825** | action affects geometry, not just texture |
| Motion-region depth win-rate ↑ | **0.7708** | the effect remains in moving regions |

For downstream policy evaluation, the current best LIBERO spatial policy chain reaches **384/500 = 76.8%** over 500 episodes. I keep this separate from reconstruction metrics because lower offline loss did not always produce higher rollout success.

## Current takeaway

WM3D is not just a video demo. It is a state-space bet:

- **Predict the future in native 3D.**
- **Treat actions as interventions on geometry.**
- **Render RGB as a view of the imagined world, not as the world state itself.**
- **Use the same state for prediction, counterfactual testing, progress scoring, action proposal, and policy evaluation.**

<a href="/wm3d/" class="btn btn--primary btn--large">View the visual project showcase →</a>
