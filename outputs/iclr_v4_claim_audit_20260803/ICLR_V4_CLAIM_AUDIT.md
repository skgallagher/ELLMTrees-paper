# `iclr_draft_v4` claim audit

Audit date: 2026-08-03

Manuscript baseline: `ELLMTrees-paper` commit `4cd0de9e726adb42a52a8158d2c7b8c33733ae1f` plus the one correction recorded below.

Scope: numerical and method claims that materially support the paper's conclusions.

## Verdict

The main topology table survives the audit. All eight fresh condition rows (five Flan-T5-base and three Llama-3.2-1B rows) match their primary 50-tree cosine summaries, including sample counts, ordering counts, rank-biserial effects, within-run branch correlations, clade recovery, PAER, RF, and FN. The Llama LoRA sources explicitly identify `artifact=adapter_chain` and `representation=lora_cumulative_delta`; the Llama full-FT source identifies `artifact=model` and `representation=full_weight`. The manuscript's corrected cumulative-update wording is therefore accurate.

The manuscript's supported result claims are now clean of known red items: the unreproducible appendix comparison was removed rather than replaced with an unvalidated number. The old reproducibility workbook remains internally inconsistent enough that it should not be used as an authority without the new ledger.

Audit totals: **26 green, 6 yellow, 3 red**. Of the three red items, one manuscript typo was corrected, one unsupported appendix result was removed pending a frozen rerun, and one is a reproducibility-workbook routing failure rather than a manuscript-number failure.

## Red findings

1. **Task-metadata baseline is not reproducible from the checked-in analysis.** Appendix text reported `n=47`, 65.6% clade recovery, and 44.7% PAER. The checked-in `scripts/taskpath_baseline.py` prints `n=50`, 61.7%, and 42.0%. Applying the paper's current NJ/scoring code to the stated 47-run eligible sample gives 77.0% and 66.0%. These are three different answers. The quantitative paragraph was removed from v4; it must not be reinstated until the intended sample, tie handling, split definition, and scorer are frozen in a compact per-run artifact.

2. **A stale fresh-suite range remained in the appendix.** The text said 82--97% for Table 2; the current shared-tree table is 88--97%. This audit corrected `82` to `88` in `iclr_draft_v4.tex`.

3. **The existing reproducibility workbook routes readers to obsolete Table-2 data.** `Claim Index` points to `T2 Recovery`, whose LoRA rows are legacy `n=40--47` analyses. The manuscript uses `T2 Shared Samples`, whose fresh rows are `n=50/26`. The workbook's README partly warns about this, but the claim index still calls the obsolete tab formula-linked coverage for the paper. Treat `T2 Recovery` as quarantined legacy data.

## Yellow findings

- **The Llama behavioral row mixes cohorts.** Translation-trained and translation-held-out cells come from historical ELLMTrees controlled trees; the four held-out probe cells come from the fresh WeightTraits full-FT production trees. The values are correct, but the shared model label can be read as a single matched experiment.
- **HellaSwag is policy-sensitive.** EOS-inclusive analysis gives `r_DL=-0.204` and is significant; excluding empty completions gives `-0.081` and is not significant. The manuscript now discloses this clearly, so this is interpretation risk rather than a hidden error.
- **The 72--94% PAER headline mixes fresh and historical controlled cohorts.** The fresh shared-tree suite is 72--92%; 94% comes from a historical Llama rank-64 cohort. The broad claim is numerically defensible only if "controlled conditions" explicitly includes historical conditions.
- **Fresh Flan sources are absent from the old workbook's source manifest.** The five primary JSONs exist locally and hash cleanly, but the workbook hashes only the new Llama summaries, not `analysis_v20260713`.
- **Important historical/organic result artifacts are not versioned in the ELLMTrees checkout.** The PhyloLM per-run CSVs and the Mistral result directory are untracked; the Figure-4 script and figure are modified. The values can be audited on this machine by hash, but a clean clone cannot recreate this evidentiary state.
- **The Wright dated result checkouts have no Git metadata.** Their contents can be hashed, but they cannot independently establish which analysis-code commit produced them.

## Green result families

- Fresh Flan and Llama topology table: exact match to primary summaries.
- LoRA representation wording: cumulative root-to-node update space, not merged-weight cosine.
- Layer-subset recovery and approximately 3% parameter claim: reproduced from all 47 matrices.
- Figure-4 diagnostics: independently recomputed over 31 plotted non-organic summaries (`Pearson=0.814/0.674`, `Spearman=0.403/0.038`) and the stated layer-coordinate bounds.
- Fresh Llama ARC-C, MMLU, TruthfulQA, Dolly, EOS sensitivity, and empty-by-depth values: exact source-table match.
- Audited regression table: matches the current recomputed coefficients, rather than the archived paper values retained in the old workbook.
- Within-task recovery: independently recomputed from the 179 eligible per-run records.
- Historical matched PhyloLM table: exact matched-sample values, clearly labeled historical in v4.
- Mistral topology: weights and PhyloLM each recover 3/3 true splits; naive sentence-embedding recovery is 1/3; informative-quartet additivity is 0.9825 versus 0.8348.

## What should be trusted now

Use `claim_ledger.csv` or the audit workbook as the routing layer. A claim is green only when the ledger names its experiment family, representation, sample, policy, primary source, and source hash. Do not route through workbook sheet names alone, and do not use an unlabeled `runs_llama*` directory as evidence.

The remaining follow-up is narrow and no longer blocks the manuscript: regenerate the task-metadata baseline into a per-run CSV with frozen eligibility and the same scorer used by the manuscript before considering whether to reinstate a quantitative comparison. Everything classed red has now been corrected, removed, or isolated.

## Source-state warning

This audit deliberately did not modify the user's pre-existing dirty files (`REPRODUCIBILITY.md`, `WeightTraits_all_results_reproducibility.xlsx`, and `iclr_draft_v2.tex`). It also did not alter dirty or untracked source artifacts in ELLMTrees, WeightTraits, or WeightTraits-free-text.
