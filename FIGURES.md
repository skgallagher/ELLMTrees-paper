# Paper Figures — Source Map

This folder is assembled by `bash scripts/assemble_paper.sh`. Re-run it whenever
results are updated on Leviathan/Ghidorah and pulled locally.

## Current Fig. 4 (updated 2026-06-29)

| File | Source | Reproduce |
|------|--------|-----------|
| `figures/fig4_coherence_atteson.png` | `/Users/shannon/Desktop/phylo/ELLMTrees/results/aggregate/recovery_rescore/fig4_atteson_layermeans.png` | In the main repo: `python scripts/make_fig4_atteson_layers.py`, then copy the output over this PNG. |

Notes:

- Main script: `/Users/shannon/Desktop/phylo/ELLMTrees/scripts/make_fig4_atteson_layers.py`.
- Panel A uses oracle bottleneck Atteson margin with the `margin=1` guarantee line.
- Panel B uses raw label-free quartet additivity `<A>`.
- Layer/adapter summaries use median bottleneck margin across runs on the x-axis and mean clade
  recovery on the y-axis; this avoids rare high-margin q/k/v outliers making weak adapter summaries
  look as if they clear the theorem threshold.
- The confusing FLAN organic point is intentionally omitted; the organic point shown is the Llama
  bush/twoclade validation.

---

## blackbox/summarization/   ← blackbox/results/figure4/
| File | Description | Status |
|------|-------------|--------|
| `nj_fingerprint.png` | Main fingerprint NJ tree — mean embedding per model | ✅ ready |
| `nj_consensus.png` | Overall consensus tree across all 500 prompts | ✅ ready |
| `nj_consensus_grid.png` | 2×5 grid of per-dataset consensus trees | ✅ ready |
| `pca_single_prompt.png` | PCA of one ArXiv prompt (Fig 4 left panel) | ✅ ready |
| `pca_fingerprint.png` | PCA of model fingerprints | ✅ ready |
| `distance_heatmap.png` | Per-prompt model-pair cosine distances | ✅ ready |

## blackbox/qa/              ← blackbox/results/figure4_qa/
| File | Description | Status |
|------|-------------|--------|
| `nj_fingerprint.png` | Fingerprint NJ tree for QA task | ⚠️ provisional |
| `nj_consensus_grid.png` | Per-dataset consensus trees | ⚠️ provisional |
| `scores_summary.csv` | Exact match / F1 per model per dataset | ⚠️ scores broken |

> ⚠️ Gemini responses are still broken (ThinkingConfig bug). Re-run after fix.
> ⚠️ Exact match = 0 for all models — scoring needs to handle verbose responses.

## blackbox/clf/             ← blackbox/results/figure4_clf/
| File | Description | Status |
|------|-------------|--------|
| `nj_fingerprint.png` | Fingerprint NJ tree for classification task | ⚠️ provisional |
| `scores_summary.csv` | Accuracy per model per dataset | ⚠️ provisional |

> ⚠️ Gemini responses broken. Re-run after fix.

## blackbox/translation/     ← blackbox/results/figure4_translation/
| File | Description | Status |
|------|-------------|--------|
| `nj_fingerprint.png` | Fingerprint NJ tree for translation task | ✅ ready* |
| `nj_consensus_grid.png` | Per-language-pair consensus trees | ✅ ready* |
| `scores_summary.csv` | BLEU scores per model per language pair | ✅ ready* |

> *Translation gives the cleanest trees — crossing the language boundary decouples
> embeddings from source content, so model-specific style dominates.
> Gemini issue less severe here than in QA/CLF.

## blackbox/cross_task/      ← blackbox/results/all_tasks/
| File | Description | Status |
|------|-------------|--------|
| `nj_fingerprint_avg.png` | **KEY FIGURE**: NJ tree from averaged fingerprint distances | ✅ ready |
| `nj_fingerprint_grid.png` | 4-panel: one fingerprint tree per task | ✅ ready |
| `nj_consensus_pooled.png` | Majority-rule over all 315+ prompts across 4 tasks | ✅ ready |
| `nj_consensus_grid.png` | Per-task consensus trees side by side | ✅ ready |
| `distance_matrix_avg.csv` | 7×7 averaged cosine distance matrix | ✅ ready |

## blackbox/st_crosscheck/   ← blackbox/results/all_tasks_st/
| File | Description | Status |
|------|-------------|--------|
| `nj_fingerprint_avg_st.png` | Same as above but all-MiniLM-L6-v2 (384-dim) | 🔄 in progress |
| `nj_fingerprint_grid_st.png` | Per-task fingerprint grid (ST embeddings) | 🔄 in progress |

> Cross-check: if ST and OpenAI trees agree, topology is embedding-agnostic.

## blackbox/scores/
Collected from each task's `scores_summary.csv`. Contains per-model, per-dataset
mean scores (exact_match / F1 / BLEU depending on task).

## whitebox/latest/          ← results/<best available run>/
| File | Description | Status |
|------|-------------|--------|
| `tree_comparison.png` | Reconstructed vs ground-truth tree comparison | ✅ ready |
| `heatmap_cosine.png` | Layer-averaged cosine distance heatmap | ✅ ready |

---

## Known issues / TODO
- [ ] Fix Gemini ThinkingConfig bug → re-collect QA/CLF → re-embed → re-analyze
- [ ] Fix QA scoring (exact_match = 0 for all models due to verbose responses)
- [ ] Wait for ST cross-check to complete on Leviathan
- [ ] Add whitebox branching_v3 results once Ghidorah finishes
- [ ] Add Llama 2 family results once Ghidorah job 3962 completes
