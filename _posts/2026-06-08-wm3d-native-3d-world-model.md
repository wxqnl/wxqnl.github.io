---
title: "WM3D: A Native-3D Action-Conditioned World Model"
date: 2026-06-08
permalink: /posts/2026/06/wm3d-native-3d-world-model/
excerpt: "Most world models predict the future in 2D video latents. WM3D moves the world state onto explicit 3D geometry — world points, camera pose, and depth — and predicts how a given action pushes that geometry forward in time."
tags:
  - world model
  - embodied AI
  - 3D
  - robot manipulation
---

Most world models predict the future in **pixel or video-latent space**. **WM3D** takes a
different route: it moves the world state onto **explicit 3D geometry** — *world points +
camera pose + depth*, read out by a frozen feed-forward 3D foundation model (VGGT) — and
predicts how a **given action chunk** pushes that geometry forward in time. For embodied
manipulation, modeling the world in 3D is the more physically grounded, more transferable
route.

![Droid prediction vs. ground truth](/wm3d/assets/demo_droid.gif)
*Left: the model's imagined future. Right: the real future. Shown from the current 1B trunk.*

**What's inside:**

- A frozen **VGGT** geometry tokenizer turns each observation into native-3D tokens.
- A **dual-stream trunk** (State Stream ⇄ Action Stream / IDM, bidirectional cross-attention)
  predicts the *future* 3D tokens, conditioned on the action chunk.
- Pluggable heads land the prediction as geometry / pixels / scores / proposals / video.
- Current scale: **~1B params, trained from scratch** on **160k Open X-Embodiment windows
  (~497 hours of real robot video, 5 embodiments)**.

The native-3D variant (world points + camera pose supervision) is the core experiment
currently in training. A frozen HunyuanVideo model, steered by a trained control adapter,
renders the model's rough prediction into detailed video — see the interactive page for the
three-way *rough → render → ground-truth* demos.

<a href="/wm3d/" class="btn btn--primary btn--large">View the full interactive page →</a>
