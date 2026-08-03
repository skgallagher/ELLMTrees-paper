# Reproducibility — historical source map

> **Submission status:** the current paper is `iclr_draft_v4.tex`; see
> [`HANDOFF_iclr_draft_v4.md`](HANDOFF_iclr_draft_v4.md) for the ICLR 2027 build, page-limit, area,
> and submission decisions. Some table numbers and removed experiments below describe earlier drafts
> and are retained only as a provenance record.

Every figure and table in the submission → the command that produces it → the expected
number. All generators live in the **experiment repo** (`../ELLMTrees/`); figures are
copied into `figures/` by `bash scripts/assemble_paper.sh`. Run generator commands from
the experiment-repo root with the `ellmtrees` conda env active.

- Full orchestration (env, clusters, pipeline, gotchas): **`../ELLMTrees/RUNBOOK.md`**.
- Data of record: `../ELLMTrees/results/aggregate/` (cross-checked here 2026-07-02).
- Compile recipe + edit log: `HANDOFF_iclr_draft.md`.

## Compile

```bash
cp iclr_draft.tex _b.tex && pdflatex -interaction=nonstopmode _b.tex && bibtex _b && \
  pdflatex -interaction=nonstopmode _b.tex && pdflatex -interaction=nonstopmode _b.tex
pdfinfo _b.pdf | grep Pages     # main text ends p9; refs p10+ don't count to the 9-pp limit
```

## Figures

| Label | File | Generator (in `../ELLMTrees/`) | Notes / expected |
|---|---|---|---|
| `fig:overview` | `fig1_paper_overview.pdf` | `figures/fig1_paper_overview.tex` (standalone TikZ, in this repo) | Schematic; no data. |
| `fig:coherence_recovery` | `fig4_coherence_atteson.png` | `scripts/make_fig4_atteson_layers.py` | Panel A oracle Atteson margin w/ margin=1 line; Panel B raw quartet additivity ⟨A⟩. |
| `fig:real-trees` | `hf_mistral_hero_3panel.png` | `codex/score_hf_zoo_recovery.py` + `codex/plot_hf_zoo_tree_vs_truth.py` | Organic Mistral `d_mistral_7b`: cosine/correlation → FN=0, **100% clade recovery** on all 3 SFT→RLHF/DPO splits; ⟨A⟩=0.98. |
| `fig:layertrace_grid` | `fig3_layertrace_grid.png` | `scripts/make_layertrace_grid.py` (per-group `scripts/make_fig3_fn.py`) | App. B. Encoder SA.k is the cleanest carrier; Pythia input-embed 3.2% FN. |
| `fig:blackbox_hero` | `hero_phylogeny_treespace.png` | `scripts/frechet_task_trees.py` → `scripts/make_hero_figure.py` | 21 models, per-prompt Fréchet (n=264). Run frechet_task_trees first. |
| `fig:frechet_grid` | `frechet_grid_tasks.png` | `scripts/make_frechet_grid.py` | Appendix. Overall + 4 per-task Fréchet trees. |
| `fig:regression_diagnostics` | `regression_diagnostics_semantic.png` | `scripts/regression_distance_vs_behavior.py --plot` | Appendix F. |

## Tables

| Label | What | Generator / source | Expected |
|---|---|---|---|
| `tab:related` | prior-work comparison | static (hand-authored) | — |
| `tab:rf_by_leaves` (Table 1) | recovery by model, leaves ≥ 5 | `scripts/rescore_recovery.py` → `results/aggregate/recovery_rescore/by_group_cosine.csv` | clade recovery: branching_v3 **90.4%**, pythia160m_full_ft **97.5%**, llama1b_base_full_ft **85.2%** |
| `tab:variants` | layer + LoRA variants | same `by_group_cosine.csv` rows + `tab:layer_subsets` | lora_qkv 83.3%, lora_k_only 81.6% (cumulative) |
| `tab:layer_subsets` | LayerTrace sparse-layer recovery | `run_analysis.py --layer_subset ...` per run, rescored | enc SA.k recovers ≥ full-weight |
| `tab:lora_variants` | LoRA branch-structure dose-response | `scripts/analyze_branch_structure.py` per group | Fisher-z r monotone: k_only −0.597 → qkv −0.670 → full-FT −0.802 |
| `tab:direct` | same- vs cross-branch (direct lineage) | `scripts/analyze_branch_structure.py` | branching_v3 Mann-Whitney p=8.9e-47, rank-biserial 0.61 |
| `tab:behavior_holdout` (Table 4) | held-out behavioral bridge | `scripts/behavior_meta_table.py` → `results/aggregate/behavior_meta_table.csv` | see below |
| `tab:regression_betas` | per-group regression β / within-run r | `scripts/regression_distance_vs_behavior.py` | Appendix F |
| `tab:translation_probe_examples` | qualitative outputs | from `behavioral_translation/predictions.json` | — |

### `tab:behavior_holdout` expected (DerSimonian-Laird RE r)

| model | probe | condition | DL r | p |
|---|---|---|---|---|
| Llama-3.2-1B full-FT | Translation | held-out (`_notrans`) | **−0.333** | 4.9e-9 |
| Llama-3.2-1B full-FT | Translation | trained | −0.294 | 1.2e-5 |
| Llama-3.2-1B full-FT | HellaSwag | held-out | −0.215 | 4.6e-4 |
| Flan-T5 full-FT | Translation | trained (`branching_v3`) | −0.260 | 3.4e-7 |
| Flan-T5 full-FT | HellaSwag | held-out (`branching_v3`) | −0.292 | 1.1e-10 |
| Flan-T5 full-FT | Translation | held-out (`no_translation_v2`) | +0.113 | 0.024 |

> The Flan `no_translation_v2` cell is **positive by design** — the MC-probe /
> single-task-per-leaf vocabulary-collapse artifact, not a clean null. The
> architecture-matched Llama held-out (−0.333) is the clean held-out result. Both are
> reported; do not "correct" the +0.113. Rationale: `../ELLMTrees/CLAUDE.md`.

## Data caveat

`results/aggregate/recovery_rescore/*.csv` must reflect the **cumulative** LoRA
measurement (the earlier increment-era numbers are falsified). If a table looks off,
confirm the CSV was regenerated with `--cumulative` (see
`../ELLMTrees/notes/cumulative_rerun_runbook.md`). Distance cubes are gitignored; the
scoring/plotting scripts need `dendropy`+`networkx`+`biopython` and, for the behavioral
paired DV, torch + sentence-transformers on the Mac/cluster.
