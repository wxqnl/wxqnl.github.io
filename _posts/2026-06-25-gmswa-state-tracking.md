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

The problem is the core of the story: full attention has strong quality but its KV cache grows linearly with context; sliding-window attention keeps cache bounded but forgets beyond the window; recurrent memory is compact and recall-friendly but can trail softmax attention on base language quality. GMSWA tests whether a window-memory hybrid can keep the best parts.

The layer runs two branches: exact sliding-window attention for recent local evidence, and a gated-delta recurrent matrix memory for long-range information. A learned per-head gate mixes the two.

![GMSWA architecture](/gmswa/assets/gmswa_architecture.png)

The latest paper draft frames the contribution as a controlled characterization rather than an overclaim. The headline findings are:

- **Base quality is preserved.** GMSWA is on par with softmax baselines on a 10-task zero-shot suite and above a pure recurrent baseline.
- **Real semantic recall improves over local-only attention.** On real recall-intensive tasks, GMSWA is comparable to the recurrent baseline: it wins on SQuAD and FDA, while trailing on SWDE.
- **The cache is constant.** At 128K context, GMSWA's KV cache is about **16.3MB**, versus about **12.9GB** for full attention — roughly **790× smaller** cache.
- **Synthetic needle retrieval is the limitation.** On NIAH, GMSWA tracks local-window attention and trails the pure recurrent model.
- **The negative result is informative.** Memory-directed controls — normalization, pathway dropout, memory-first curriculum, and memory-only training — all hurt or collapse recall, suggesting the issue is limited sharp addressing rather than simply “the model ignored memory.”

Core benchmark snapshot:

| Model | Zero-shot avg ↑ | NLL 1–2K ↓ | NLL 2–4K ↓ | NLL 4–8K ↓ |
| --- | ---: | ---: | ---: | ---: |
| Transformer | **0.500** | **3.62** | 5.74 | 7.21 |
| SWA | 0.498 | 3.87 | 4.14 | 4.21 |
| **GMSWA** | **0.499** | 3.78 | **4.02** | **4.08** |
| GDN | 0.486 | 3.89 | 4.15 | 4.24 |

Detailed zero-shot suite:

| Model | Avg ↑ | LAMBADA | PIQA | HellaSwag | WinoGrande | ARC-e | ARC-c | BoolQ | COPA | OBQA | SciQ |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Transformer | **0.500** | 0.317 | **0.660** | 0.330 | 0.523 | 0.569 | 0.253 | **0.615** | **0.700** | 0.206 | 0.822 |
| SWA | 0.498 | 0.315 | 0.658 | 0.326 | 0.510 | **0.577** | 0.241 | 0.608 | **0.700** | **0.240** | 0.803 |
| **GMSWA** | **0.499** | **0.324** | 0.655 | **0.330** | **0.530** | 0.566 | **0.258** | 0.611 | 0.670 | 0.202 | **0.849** |
| GDN | 0.486 | 0.291 | 0.656 | 0.324 | 0.509 | 0.561 | 0.241 | 0.588 | 0.650 | 0.218 | 0.819 |

MMLU is not shown because it was not part of this controlled run's completed result set; the available standard suite includes HellaSwag, PIQA, ARC, BoolQ, COPA, OpenBookQA, SciQ, WinoGrande, and LAMBADA.

| Model | SWDE ↑ | FDA ↑ | SQuAD ↑ | KV cache @128K |
| --- | ---: | ---: | ---: | ---: |
| SWA | 0.072 | 0.040 | 0.072 | 50MB |
| **GMSWA** | 0.088 | **0.094** | **0.284** | **16.3MB** |
| GDN | 0.110 | 0.026 | 0.274 | 3.4MB |
| Transformer | **0.438** | **0.156** | 0.066 | 12.9GB |

The short version: **GMSWA is a practical constant-cache long-context model and a clean map of where window–memory hybrid recall works — and where it stops.**

<a href="/gmswa/" class="btn btn--primary btn--large">View the visual project showcase →</a>
