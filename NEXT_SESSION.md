# Read this first next session

Open `HANDOFF_FIGURE2_20260804.md` and inspect `figures/fig4_coherence_atteson.pdf`.

Figure 2 is rebuilt with full-FT, merged-LoRA, subset, single-matrix, and matched PhyloLM estimates.
The pooled fractional-logit fits are dark dashed curves through all 400 displayed estimator summaries.
Each point averages its diagnostic and recovery over the same 46 eligible trees.

First decision next time: whether the descriptive fractional-logit curves are sufficient for the
paper or whether to add a joint tree bootstrap that resamples the 46 tree IDs, recomputes all estimator
means, and refits the slope while preserving paired dependence.
Also compile the manuscript in a TeX-enabled environment and verify the Figure 2 caption/page layout.
