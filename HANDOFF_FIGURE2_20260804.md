# Figure 2 handoff — 2026-08-04

## What changed

Figure 2 (`fig4_coherence_atteson`) was rebuilt in the old paper style: every mark is a
summary averaged over a simulation rather than a separate tree/run observation. The y-axis is
mean true clades recovered (%) over the common 46 topology-eligible trees.

The final figure contains 400 summary points:

- 8 whole-weight simulation estimates;
- 3 matched behavior-only PhyloLM estimates;
- 29 post-hoc projection-subset estimates; and
- 360 individual q, k, or v matrix estimates.

The eight whole-weight simulations include Flan-T5 full fine-tuning, four Flan merged-LoRA
conditions, Llama-3.2-1B full fine-tuning, and Llama merged-LoRA rank 8 and rank 64. Full
fine-tuning is therefore present for both architectures. LoRA topology estimates use sequentially
merged, standalone full-weight checkpoints.

The three fresh matched PhyloLM points use the exact same 46 eligible Llama topology IDs:

| Condition | Recovery | Additivity (all quartets) | Atteson diagnostic |
|---|---:|---:|---:|
| Llama LoRA q/k/v, rank 8 | 34.78% | 0.575 | 0.084 |
| Llama LoRA q/k/v, rank 64 | 23.91% | 0.607 | 0.093 |
| Llama full fine-tuning | 51.99% | 0.627 | 0.206 |

PhyloLM uses a filled diamond and inherits its matched Llama condition color. The dark
navy/charcoal diamond is full fine-tuning, gold is rank 8, and orange-red is rank 64. Whole-weight
simulations are filled circles, subsets are hollow squares, and q/k/v matrices use small translucent
circle/triangle/square marks.

Both panels now include a dark dashed pooled fractional-logit curve. The response is mean recovery
proportion and the model is a quasi-binomial logit weighted by `n_runs` (46 for every point). The fit
uses the 40 simulation, subset, and PhyloLM summaries. The 360 individual matrices remain visible but
are excluded from the fit so layer multiplicity does not overwhelm the higher-level summaries.

## Scientific interpretation

Adding PhyloLM expands the observed recovery range from the weight-only ceiling (roughly 71–99% for
individual matrices and 87–98% for whole simulations) down to 24–52%. Both diagnostics now show a
clear positive, saturating empirical relationship with recovery. This is much more informative than
a pooled correlation among ceiling-compressed weight estimates.

The dashed curves are still descriptive calibration, not a formal independence-aware significance
test. Subsets and matrices reuse the same 46 trees and are correlated. If formal inference is needed,
the next analysis should operate on per-tree records and use a tree-clustered bootstrap or a
mixed-effects/binomial model.

## Source and generated files

WeightTraits:

- `scripts/build_merged_weight_figure2.py` — builds all 400 summary points from rollups and saved
  distance cubes, plus the fresh PhyloLM per-run metrics.
- `reports/paper/merged_weight_figure2.csv` and `.json` — generated data (locally ignored).

ELLMTrees:

- `scripts/ggplot/make_merged_weight_figure2.R` — shared-theme ggplot renderer and fractional-logit
  fits.
- `scripts/ggplot/theme_weighttraits.R` — shared publication theme.
- `results/aggregate/weighttraits_fresh_phylolm/per_run_metrics.csv` — fresh matched PhyloLM source.
- `results/aggregate/weighttraits_fresh_phylolm/table_metrics.csv` — archived aggregate source table.

ELLMTrees-paper:

- `figures/fig4_coherence_atteson.pdf`
- `figures/fig4_coherence_atteson.png`
- `iclr_draft_v4.tex` — Figure 2 results paragraph and caption updated.
- `FIGURES.md` and `REPRODUCIBILITY.md` — provenance updated.

## Reproduction

From `/Users/shannon/Desktop/phylo/WeightTraits` in the `ellmtrees` environment:

```bash
PYTHONPATH=src python scripts/build_merged_weight_figure2.py \
  --weighttraits-root . \
  --out reports/paper/merged_weight_figure2.json \
  --csv-out reports/paper/merged_weight_figure2.csv
```

Then:

```bash
Rscript /Users/shannon/Desktop/phylo/ELLMTrees/scripts/ggplot/make_merged_weight_figure2.R \
  /Users/shannon/Desktop/phylo/WeightTraits \
  /Users/shannon/Desktop/phylo/ELLMTrees-paper
```

## Validation completed

- Data validation: 400 rows total; all `n_runs=46`; 397 weight summaries and 3 PhyloLM summaries.
- Exact PhyloLM topology-ID intersection checked against the Llama weight rollup (46/46, no mismatch).
- `ruff check scripts/build_merged_weight_figure2.py` passed.
- R rendering completed without warnings.
- PDF checked as a one-page R 4.5.2 artifact, 514 × 342 points.
- PNG was visually inspected after the final dashed-line render; axes, symbols, error bars, and legends
  are not clipped.
- TeX was not compiled locally because a TeX installation is unavailable in this workspace.

## Working-tree warning

All three repositories contain substantial unrelated local changes. Only the Figure 2 files listed
above should be staged or committed for this work. Do not use `git add -A`.
