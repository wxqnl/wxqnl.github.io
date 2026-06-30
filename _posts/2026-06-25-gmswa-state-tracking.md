---
title: "GMSWA: Constant-Cache Long-Context Memory"
date: 2026-06-25
permalink: /posts/2026/06/gmswa-state-tracking/
excerpt: "GMSWA fuses exact sliding-window attention with a recurrent matrix memory. In a controlled 340M study, it preserves softmax-like base quality, recovers real semantic recall, and keeps a constant cache, while exposing a sharp synthetic-recall limitation."
tags:
  - long context
  - attention
  - language modeling
  - recurrent memory
---

**GMSWA** (Gated-Memory Sliding-Window Attention) studies a practical long-context question:

> Can a model keep the language quality of local/softmax attention, recover useful long-range recall, and avoid a growing KV cache?

The layer runs two branches: exact sliding-window attention for recent local evidence, and a gated-delta recurrent matrix memory for long-range information. A learned per-head gate mixes the two.

![GMSWA architecture](/gmswa/assets/gmswa_architecture.png)

The latest paper draft frames the contribution as a controlled characterization rather than an overclaim. The headline findings are:

- **Base quality is preserved.** GMSWA is on par with softmax baselines on a 10-task zero-shot suite and above a pure recurrent baseline.
- **Real semantic recall improves over local-only attention.** On real recall-intensive tasks, GMSWA is comparable to the recurrent baseline: it wins on SQuAD and FDA, while trailing on SWDE.
- **The cache is constant.** At 128K context, GMSWA's KV cache is about **16.3MB**, versus about **12.9GB** for full attention — roughly **790× smaller** cache.
- **Synthetic needle retrieval is the limitation.** On NIAH, GMSWA tracks local-window attention and trails the pure recurrent model.
- **The negative result is informative.** Memory-directed controls — normalization, pathway dropout, memory-first curriculum, and memory-only training — all hurt or collapse recall, suggesting the issue is limited sharp addressing rather than simply “the model ignored memory.”

The short version: **GMSWA is a practical constant-cache long-context model and a clean map of where window–memory hybrid recall works — and where it stops.**

<a href="/gmswa/" class="btn btn--primary btn--large">View the visual project showcase →</a>
