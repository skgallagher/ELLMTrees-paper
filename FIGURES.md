# Paper Figures — Source Map

This folder is assembled by `bash scripts/assemble_paper.sh`. Re-run it whenever
results are updated on Leviathan/Ghidorah and pulled locally.

## Current manuscript figures (refresh 2026-08-04)

| File | Source | Reproduce |
|------|--------|-----------|
| `figures/fig4_coherence_atteson.pdf` | full-FT plus fresh merged-weight Flan/Llama LoRA run, subset, and single-matrix summaries, with matched Llama PhyloLM estimates (46 eligible trees per point) | run the Figure 2 builder and ggplot renderer listed in `HANDOFF_FIGURE2_20260804.md` |
| `figures/hf_mistral_hero_3panel.pdf` | documented truth, weight Fr\'echet, and PhyloLM Newick trees | same command |
| `figures/fig3_layertrace_grid.pdf` | per-layer RF summaries for Flan-T5 and Llama-3.2-1B | same command |
| `figures/regression_diagnostics_semantic.pdf` | six per-pair regression CSVs in `results/aggregate/` | same command |

Notes:

- Figure 2 data are built by `WeightTraits/scripts/build_merged_weight_figure2.py` from the full-FT
  and fresh merged-weight distance cubes plus the fresh matched PhyloLM per-run metrics, then rendered
  by `ELLMTrees/scripts/ggplot/make_merged_weight_figure2.R` with the shared publication theme.
- `scripts/export_ggplot_data.py` reuses the existing Python analysis functions and exports stable,
  tidy CSVs. It does not alter or duplicate the scientific calculations.
- `scripts/ggplot/make_paper_figures.R` owns presentation through one shared publication theme.
- R dependencies are `ggplot2`, `dplyr`, `readr`, `tidyr`, `ggrepel`, and `patchwork`.
- Both vector PDFs (used by LaTeX) and high-resolution PNG previews are generated.
- The color palette is color-vision-deficiency safe; critical categories also differ by shape.
- The TikZ overview remains vector TikZ and is intentionally outside the ggplot pipeline.

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
