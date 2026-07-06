# Handoff — ICLR draft v2 (`iclr_draft_v2.tex`)

_Last writing session: 2026-07-06. Repo: `ELLMTrees-paper`._
_**Next session: start on §3 Framework.**_

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

## What changed this session (all pushed; last conflict resolved keeping HEAD)

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
1. **Table 1 vs prose endpoints.** The **Exact rec.** column spans **74–95%** (Flan-qkv 74 → Pythia
   95), but the abstract/intro/§5 summary say **72–94%**. Pick one set of endpoints and make the table
   and all prose agree.
2. **"Atteson margin" styling** — standardize (no hyphen, lowercase "margin") everywhere; earlier
   spots had "Atteson Margin" / "Atteson-Margin."
3. **"behavior only" vs "weight–behavior"** in the opening list look similar; headers disambiguate, but
   keep an eye on it while reading §3–§5.

## §3 Framework — where to pick up next
Starts at `\section{Framework}` (~line 197). Current content defines: single-root assumption
(cf. `yu2025neural`, unlike `horwitz2025mother`), distance matrix + neighbor-joining
(`saitou1987neighbor`), and the phylogeny/clade explainer. Do the same **honest / accurate /
compelling / sounds-like-Shannon** pass we did on the intro. Specific things to check in §3:
- Keep the **clade vs exact recovery** distinction consistent with the abstract/Table 1 fix above.
- Atteson-margin definition + the label-free proxy **A** (four-point additivity; `A = 1 − δ`,
  cites `buneman1971recovery`, `holland2002delta`) — make sure the §2 closing "margin > 1 ⇒ recovers"
  claim lines up with how §3 defines the margin.
- Permutation-test / SNR framing (points to appendix) — trim to voice, avoid over-formal restatement.
- Terminology discipline: "estimate" = the act, "recovery" = the metric.
