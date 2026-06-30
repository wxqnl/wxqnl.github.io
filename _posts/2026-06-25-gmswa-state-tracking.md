---
title: "GMSWA: Memory that Tracks State"
date: 2026-06-25
permalink: /posts/2026/06/gmswa-state-tracking/
excerpt: "GMSWA separates long context into retrieval and state tracking. Sliding-window attention handles local evidence; recurrent memory carries the hidden state; a memory-aware gate prevents the hybrid from collapsing to local shortcuts."
tags:
  - long context
  - attention
  - language modeling
  - state tracking
---

**GMSWA** is a long-context language modeling project built around a simple thesis:

> Retrieval and state tracking are different capabilities.

Sliding-window attention is useful for local evidence and retrieval, but it struggles to maintain a hidden state once the update chain leaves the window. Recurrent memory can carry that state — but a naive hybrid often learns the easy local-attention shortcut and suppresses the memory branch.

The public story is capability-first, not experiment-name-first:

1. Local-window and depth-limited attention collapse on long-range state tracking.
2. Recurrent memory solves the state update primitive.
3. Naive hybrids still fail because the learned gate collapses to local attention.
4. A memory-aware gate initialization restores the state-tracking path.
5. Retrieval remains roughly preserved while scaled language-model validation continues.

Current highlights:

- **Long-range flip tracking:** GMSWA reaches 1.00 where local-only and naive-hybrid variants fall to chance.
- **Compositional state tracking:** GMSWA remains strong beyond four windows.
- **Retrieval axis:** retrieval benchmark performance is roughly preserved.
- **Scaled validation:** a 340M language-model check is validating that the final recipe stays stable beyond synthetic tasks.

<a href="/gmswa/" class="btn btn--primary btn--large">View the visual project showcase →</a>
