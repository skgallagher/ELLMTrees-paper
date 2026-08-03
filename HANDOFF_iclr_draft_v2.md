# Handoff — ICLR draft v2 (`iclr_draft_v2.tex`)

> **Historical document.** The current submission file and settled ICLR 2027 decisions are documented
> in [`HANDOFF_iclr_draft_v4.md`](HANDOFF_iclr_draft_v4.md). Do not treat the v2 open items or page
> counts below as the current submission state.

_Last writing session: 2026-07-07 (§5 Results + §6 Discussion pass — COMPLETE). Repo: `ELLMTrees-paper`._
_**Next session: draft is complete through all sections. Only open item is the 3 Flan MC probe cells in
Table 3 (Ghidorah jobs pending) — see "Open items (2026-07-07)" below.**_

## Working file
**`iclr_draft_v2.tex`** is the live draft (NOT `iclr_draft.tex` — that's the older v1 trim;
`HANDOFF_iclr_draft.md` describes v1 and is stale). Shannon edits live in **Overleaf**, synced to
GitHub. Figure 1 is a standalone TikZ: `figures/fig1_paper_overview.tex` → `fig1_paper_overview.pdf`
(the draft `\includegraphics` the PDF, so **the .tex must be recompiled to PDF after any figure edit**,
and both committed).

## Compile / check
```bash
cd ~/Desktop/phylo/ELLMTrees-paper
cp iclr_draft_v2.tex _b.tex && pdflatex -interaction=nonstopmode _b.tex && bibtex _b && \
  pdflatex -interaction=nonstopmode _b.tex && pdflatex -interaction=nonstopmode _b.tex
# Figure 1 (after editing its .tex):
cd figures && pdflatex -interaction=nonstopmode fig1_paper_overview.tex   # regenerates the .pdf
```

## Overleaf ↔ GitHub sync (bit us this session)
Editing in **both** Overleaf and local git on the same lines makes Overleaf open a conflict branch
(`overleaf-YYYY-MM-DD-HHMM`) that won't auto-merge. To resolve:
```bash
gitfix
cd ~/Desktop/phylo/ELLMTrees-paper
git fetch origin && git checkout main
git merge origin/overleaf-<branch>     # resolve <<<< ==== >>>> markers in the file(s) listed
git add <resolved files> && git commit --no-edit && git push
# then Overleaf → click "continue"
```
Lead every git block with `gitfix` (clears the recurring `.git/index.lock`). Don't `git add -A` —
`paper.tex`, `.DS_Store`, some PNGs, and untracked `outputs/`/`results/` are left alone on purpose.

## Session 2026-07-07 — §5 Results + §6 Discussion pass (COMPLETE)

Paragraph-by-paragraph + table-by-table through §5 and §6. Draft now **17pp**, compiles clean (no
undefined refs). Cowork sandbox made the edits; Shannon pushed each from her Mac. Sandbox needed
`scipy`/`dendropy`/`biopython` via `pip install --target=/tmp/pylibs` (PyPI works; `/sessions` disk is
100% full so install to `/tmp`; run figure scripts with `PYTHONPATH=/tmp/pylibs MPLCONFIGDIR=/tmp/mpl`).

**Recovery table restructure.**
- **Deleted old Table 1 `tab:rf_by_leaves`** (by-architecture) — recovery now lives only in `tab:variants`.
  Repointed its orphaned refs (task-baseline app) to `tab:variants`; bumped one cited Flan full-FT exact
  84→85% to match that row. This is why table *numbers shifted*: `tab:variants` is now **Table 2**,
  `tab:behavior_holdout` is now **Table 3**.
- **`tab:variants` rebuilt:** ordering columns = **rank-biserial (SE)** + **within-run r (SE)** (was SD);
  RF/FN merged to one `RF (SE); FN (SE)` column; caption notes a red convention for non-sig ordering
  cells (none fire). All numbers recomputed from per-run CSVs, reproduce prior values.
  ⚠️ **Llama qkv r8 ordering must use `runs_llama1b_base_qkv_branch_structure.csv` (the `runs_`-PREFIXED
  file = cumulative, r=−0.94). The UNPREFIXED `llama1b_base_qkv_branch_structure.csv` is stale increment
  (r=−0.16) — do not use.**
- **Dropped appendix `tab:direct`** (folded into `tab:variants`).

**Fig `fig:coherence_recovery` (`fig4_coherence_atteson.png`) regenerated on CUMULATIVE data.**
The paper copy was the stale "Layer summaries" version with the Llama q/k/v adapter cluster pinned at
margin≈0.03 / clade≈25% = the **increment artifact** (debunked dead zone), contradicting Table 2's 94%.
Edited `ELLMTrees/scripts/make_fig4_atteson_layers.py` to **drop the r64 adapter-matrix scatter** (local
r64 per-layer cube is Jun-23 INCREMENT, mean layer dist 0.79) + its 2 legend entries; kept Llama full-FT
per-layer points (valid cumulative) + Flan subsets + group points + organic. Reran →
`results/aggregate/recovery_rescore/fig4_atteson_layermeans.png` → copied to
`figures/fig4_coherence_atteson.png`. Margin prose rewritten around norm/embed being the weak layers.
⚠️ **No cumulative Llama-qkv per-layer cube is local** (r8 never pulled; r64 is increment). To restore the
single-adapter points, recompute an r8/r64 `--cumulative` cube on the cluster + pull.

**New appendix `app:withintask`:** single-task groups recover clade **59–87%** (Flan full-FT, one task
type per tree; `recovery_rescore/per_run_cosine.csv`, groups `runs_*_only`). Corrected an in-text
"80–90+%" → "59–87%".

**Behavior bridge `tab:behavior_holdout` (Table 3):** cells = **DL r with 95% CI stacked** (`\shortstack`);
corner labeled **"DL r [95% CI]"**; p-value stars/legend dropped. **I² moved to new appendix table
`tab:behavior_het`** (r, 95% CI, I², k). All 9 cells recomputed locally from
`regression_<group>_<probe>_semantic_paired_practical.csv` — reproduce the table exactly. Flan reversal
(+0.11‡) = catastrophic forgetting, verified vs `app:translation_probe`.
⚠️ **3 Flan MC cells still `---`** (ARC-C/MMLU/TruthfulQA): only degenerate `no_translation_v2` versions
exist; `branching_v3` versions NOT collected. **Ghidorah jobs 33822 (arc), 33826 (mmlu), 33827
(truthfulqa)** launched this session. When done: pull → `python scripts/build_paired_semantic.py --group
results/runs_branching_v3` → the 3-probe regression loop → send CSVs to Cowork → it computes r+I² and
fills cells (expected to collapse — single-task-per-leaf).

**Behavior-only §5 — frontier DROPPED (Shannon: "RIP").** Cut the 23-model frontier from §5 AND the
appendix (`app:blackbox` Model-collection/Prompts/Embedding/Aggregation paragraphs + `fig:blackbox_hero`
+ `fig:frechet_grid`); moved `billera2001geometry`+`willis2019confidence` (cited only there) to the §3
Fréchet-mean line; kept the PhyloLM-reproduction paragraph. Centered on **PhyloLM** + added the
**controlled comparison** (diary 2026-07-03): weights vs PhyloLM clade — held-out full-FT **95% vs 57%**,
r=8 LoRA **94% vs 35%** (PhyloLM at random-tree floor); Mistral = tie (Ā 0.98 vs 0.84). Regenerated the
**Mistral hero figure** (`hf_mistral_hero_3panel.png`) with short leaf labels + fontsize 13 via
`ELLMTrees/codex/plot_hf_zoo_tree_vs_truth.py` (one-line label change: `label.split("__")[-1]`).

**Full pass fixes:** stale fig caption ("q/k/v adapter matrices" → "full-FT layers"), removed broken
`app:robustness` ref, Discussion exact-recovery 79→72–94%, typos; Discussion smoothing + added the
additivity-proxy sentence ("Because the additivity proxy A requires no ground-truth tree…").

**Code changed in the `ELLMTrees` (experiments) repo — push separately from the paper repo:**
`scripts/make_fig4_atteson_layers.py`, `codex/plot_hf_zoo_tree_vs_truth.py`.

## Open items (2026-07-07)
1. **3 Flan MC probe cells** (Table 3) — Ghidorah jobs 33822/33826/33827; workflow above. **Only real
   open writing item.**
2. **More controlled PhyloLM groups next week** (Shannon) — lora_qkv/k_only/r-sweep/pythia per
   `ELLMTrees/codex/HANDOFF_phylolm_controlled.md`; could grow into a whitebox-vs-PhyloLM table in §5/App F.
3. **Optional:** restore Llama q/k/v single-adapter points in `fig:coherence_recovery` (needs a cumulative
   r8/r64 per-layer cube from the cluster).

## Session 2026-07-06 (later) — §3 Framework + §4 Experiments pass

Went paragraph-by-paragraph through §3, then §4. All edits applied to `iclr_draft_v2.tex`; each was
pushed by Shannon from her Mac (sandbox can't push over SSH). Compiles clean (`pdflatex` exit 0, no
undefined refs).

**§3 Framework — done:**
- Rewrote the intro paragraph (definitions first; dropped the muddled "LLM DNA" clause; kept the
  single-root distinction; `\citet` not `\citep` for the like/unlike).
- §3.1 RF/FN paragraph rewritten: crisp polytomy → forced-FP → report FN logic; added
  `clade recovery = 1 − FN/|true splits|`.
- §3.2 Distance metric: "three reasons" framing, fixed the norm/similarity slip, cosine distance =
  1 − cosine sim; **restored the "merged weights" definition** (base + accumulated low-rank update
  along root-to-node path) after an edit had orphaned the term.
- §3.3 fixed the garbled layer-importance sentence (rank layers by lineage signal, not "change across
  runs"); notation `\widehat{\mathbf{D}}`, accented `Fr\'echet`.
- §3.4 (retitled **"When estimation succeeds"**): fixed the broken Mann–Whitney sentence; broke up
  run-ons; **corrected a real math error** — `A = 0` is the *maximally non-additive* quartet
  (`s2 = s3`), NOT "star-like" (a star is `s1=s2=s3`, degenerate, and actually additive). Same
  imprecise "star-like" wording is still in `scripts/fourpoint_additivity.py` docstring line ~18
  (math is fine, only the comment) — worth fixing in the code repo.
- **Verified both theorems against sources** (Atteson `atteson1999performance`; four-point/δ-score
  `holland2002delta` via ape/phangorn docs). Atteson stated over *internal* edges = defensible
  refinement of the all-edges original; `A = (s2−s3)/(s1−s3) = 1−δ` confirmed correct.
- Added a sentence noting we report `A` over **informative** quartets (those the truth resolves) for
  known-truth evaluation, vs all quartets label-free — this reconciles the §3 definition with the
  headline **A=0.98 (informative)** vs **0.86 (all 210)** in the Mistral results.
- **Structural, to save space:** converted all four `\subsection`s → **bold run-in headers**
  (`\textbf{...}`), and Atteson's theorem + four-point additivity → **inline italic** statements
  (dropped the `theorem` environment). Cross-ref labels kept; `sec:framework:{metric,phylo,theory}`
  now resolve to "§3" (was §3.x) — fine in every citing spot. `sec:framework:layers` was unreferenced,
  dropped.

**Naming decision (Shannon):** the ICLR paper `yu2025neural` is titled **"Neural Phylogeny"** (not
"Neural Lineage" = the CVPR `yu2024neurallineage`). **Use the ICLR paper as base and call it "Neural
Phylogeny" in-text.** Renamed all in-text "Neural Lineage" → "Neural Phylogeny" (§3, Related-Work
Table 1, §5 direct-lineage para) and dropped the redundant `yu2024neurallineage` co-cite in §5.
`yu2024neurallineage` is now uncited in the draft (harmless; re-add if you want the CVPR paper as
distinct related work).

**§4 Experiments — done:** mostly fine already. Deleted a stale commented mixed-effects regression
block + `% NOTE` under "Predicting held-out behavior." Fixed typos (`LLama`→`Llama`, double spaces).
**Dropped the 23-model frontier probing experiment** (Shannon: "we don't use that any more") — rewrote
the behavior-only paragraph (retitled **"Comparison to behavior-only estimation"**) around the
PhyloLM-vs-weights comparison on the real Mistral family with ground truth. Abstract + Intro already
center this PhyloLM story, so they're consistent.

**Prose prefs confirmed this session (Shannon):** **no em-dashes** (use commas/colons/parens) and
**break up run-ons**. Applied throughout; keep applying.

## What changed the previous session (all pushed; last conflict resolved keeping HEAD)

**Figure 1 — full revamp** (`figures/fig1_paper_overview.tex`):
- **Panel A** (controlled recovery): added a one-way **weights → NJ** arrow (true tree → recovered),
  so weights are named there, not in the caption; **clade recovery** sits under the RF/FN compare
  arrow; bottom caption trimmed to "neighbor-joining estimate, scored against known truth."
- **Panel B** (weights predict behavior): **DerSimonian–Laird r̄ < 0** shown as a subtitle; dropped
  the bare `r<0`.
- **Panel C** — **replaced** the old frontier-blackbox cladogram with an **Atteson-margin** plot:
  two curves, weights (solid) above behavior (dashed), converging at high margin. Labels sit off the
  curves; no glib "≥" annotation (the graph implies it).
- **Banner** reworded: "…a model *phylogeny* that we estimate and compare to known phylogenies 𝒫,
  link to behavior, and read more reliably from weights than from behavior alone."
- Bottom access-axis third foot → "margin gates recovery."
- ⚠️ **Revamping Panel C dropped the frontier/blackbox preview from the overview.** It still lives in
  `fig:real-trees` (Mistral hero) and `fig:blackbox_hero`. Banner + axis were updated to match. If we
  want frontier back in the overview, revert Panel C + banner + axis (old TikZ is in git history).

**Abstract + Intro — recovery metric fixed (important):**
- The paper's Table 1 has **two** columns: **Clade rec.** and **Exact rec.** The `72–94` number is the
  **exact-recovery** stat (fraction of runs that recover the *whole* tree), NOT clade recovery. Clade
  recovery is **82–96%**. Earlier drafts mislabeled `72–94` as "clade recovery."
- Abstract now: "recovering the complete tree exactly in **72–94% of runs (82–96% of clades)**."
- Intro contribution bullet 1 matched: "recover the complete phylogeny exactly (72–94% of runs;
  82–96% of clades)."
- Fig 1 caption trimmed to a standalone one-liner (dropped the stale "three levels of access").

**Related Work — edited with Shannon:**
- Renamed the three forms → **model parameters/weights · weight–behavior · behavior only** (opening
  list + all three bold section headers).
- **CKA** spelled out ("centered kernel alignment, CKA") and cited `kornblith2019similarity` (already
  in the bib).
- **Table 1 reference moved up** to the opening paragraph ("contrasts the closest methods along the
  axes that matter"); removed the Claude-y re-enumeration of the table in the closing paragraph.
- Added the **behavior-only advantage**: "needs no shared architecture and can compare models across
  families and sizes — an advantage weight-based methods lack."
- Made the **Cloud ↔ Atteson-margin** link explicit: Cloud et al.'s "large enough training signal"
  guarantee becomes, in our setting, the Atteson-margin condition (margin > 1 ⇒ NJ provably recovers
  the split).

## Open items / watch before/at next session
0. **⚠️ FINISH THE 23-MODEL REMOVAL (top priority — §4 is now ahead of §5/appendix).** §4 no longer
   describes the 23-model frontier tree, but it's still present in:
   - §5 Results, the "Behavior-only lineage (frontier models)" paragraph (~line 594: "we estimate
     lineage from 23 frontier…", per-task RF 0.65–0.95).
   - Appendix blackbox subsection (~lines 806–857): "Model collection" para, the BHV Fréchet
     description, and **two figures** `fig:blackbox_hero` (`frechet_grid`? no — `blackbox_hero.png`)
     and `fig:frechet_grid` (`frechet_grid_tasks.png`).
   Decide: cut it entirely, or demote to a one-line appendix mention. Note `billera2001geometry` /
   `willis2019confidence` are cited **only** in that appendix (line ~828) — they become orphaned if you
   cut it, UNLESS you move them to §3.3 where the weight-based Fréchet mean is introduced (arguably
   where they belong now). Abstract/Intro already avoid the 23-model tree, so no change needed there.
1. **Table 1 vs prose endpoints.** The **Exact rec.** column spans **74–95%** (Flan-qkv 74 → Pythia
   95), but the abstract/intro/§5 summary say **72–94%**. Pick one set of endpoints and make the table
   and all prose agree.
2. **"Atteson margin" styling** — standardize (no hyphen, lowercase "margin") everywhere; earlier
   spots had "Atteson Margin" / "Atteson-Margin."
3. **"behavior only" vs "weight–behavior"** in the opening list look similar; headers disambiguate, but
   keep an eye on it while reading §3–§5.

## §5 Results — where to pick up next
§3 and §4 are done (this session). Next is a paragraph-by-paragraph pass on **§5 Results** with the
same honest / accurate / sounds-like-Shannon lens. Before/while doing it:
- **Do item 0 first** (23-model removal) so §5 matches §4.
- Keep **clade vs exact recovery** endpoints consistent with Table 1 (item 1).
- Terminology discipline: "estimate" = the act, "recovery" = the metric.
- The four-point proxy **A** framing settled in §3: label-free A over all quartets is the practitioner
  proxy; A over *informative* quartets is the known-truth eval number (0.98 vs 0.86 for Mistral). Make
  sure §5 quotes the matching one in each spot.
- Watch for leftover em-dashes in §5 (Shannon: none) and run-on sentences.

## §3 Framework — DONE 2026-07-06 (kept for reference)
Full paragraph-by-paragraph pass complete (see "Session 2026-07-06 (later)" above). Structure is now
bold run-in headers + inline italic theorem/additivity. If revisiting: the `A=0` = maximally
non-additive (not star-like) correction and the informative-quartet reconciliation are the two
substantive things to preserve.
