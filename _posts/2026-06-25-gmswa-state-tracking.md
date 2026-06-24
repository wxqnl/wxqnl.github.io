---
title: "GMSWA: Memory for State Tracking"
date: 2026-06-25
permalink: /posts/2026/06/gmswa-state-tracking/
excerpt: "GMSWA reframes long context as two jobs: retrieval and state tracking. Sliding-window attention handles local retrieval; recurrent memory carries the hidden state; a memory-biased gate prevents the hybrid from collapsing to local shortcuts."
tags:
  - long context
  - attention
  - language modeling
  - state tracking
---

**GMSWA** is a long-context language modeling project built around a simple thesis:

> Retrieval and state tracking are different capabilities.

Sliding-window attention is useful for local evidence and retrieval, but it struggles to maintain a hidden state once the update chain leaves the window. Recurrent memory can carry that state — but a naive hybrid often learns the easy local-attention shortcut and suppresses the memory branch.

The key result is a clean failure-to-fix story:

1. SWA / Transformer collapse on long-range state tracking.
2. β∈(0,2) recurrent memory solves the state update primitive.
3. Naive GMSWA still fails because the learned gate collapses to SWA.
4. Forcing memory restores performance, proving memory was capable.
5. A one-line gate bias (`mix_gate_logit_bias=-4`) makes the learned hybrid track state.

Current highlights:

- **Parity long range:** gate-bias GMSWA reaches 1.00 where SWA and naive GMSWA fall to chance.
- **S₃ group composition:** gate-bias GMSWA remains strong beyond 4 windows.
- **Retrieval axis:** v10ms roughly matches baseline NIAH / recall, so the memory knobs do not obviously hurt retrieval.
- **Scale check:** 340M v11gb run is validating the final recipe on real LM training.

<a href="/gmswa/" class="btn btn--primary btn--large">View the visual project showcase →</a>
