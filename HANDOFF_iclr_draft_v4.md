# ICLR 2027 submission handoff — `iclr_draft_v4.tex`

_Final paper-writing status: complete as of 2026-08-03._

## Submission file

- The submission source is **`iclr_draft_v4.tex`**.
- `iclr_draft_v3.tex` is the immediately preceding draft and is retained for comparison.
- V4 uses the official `iclr2027_conference.sty` and `iclr2027_conference.bst` files distributed by
  ICLR. Do not revert it to the 2026 style.
- The manuscript remains anonymous. Author acknowledgments are intentionally omitted for double-blind
  review.

## Venue and deadlines

- Venue: [ICLR 2027](https://iclr.cc/Conferences/2027)
- Abstract deadline: **2026-09-18 AOE**
- Full-paper deadline: **2026-09-25 AOE**
- Conference: **2027-04-26 through 2027-04-30**
- Official instructions: [ICLR 2027 Author Guidelines](https://iclr.cc/Conferences/2027/AuthorGuidelines)

The abstract submitted by the abstract deadline must be genuine. All authors should have current
OpenReview profiles before registration, and no authors may be added after the abstract deadline.

## Page-limit status

- The submission limit is **9 pages of main text**.
- In the authoritative Overleaf/`pdflatex` build, the main text ends on page 9.
- The required AI-use statement extends onto page 10. This is compliant: the AI-use statement does
  not count toward the page limit.
- References and appendices also do not count toward the main-text limit.
- No trimming is needed solely because the AI-use statement begins or continues on page 10.

Use the Overleaf/`pdflatex` build for final page-limit verification. The Tectonic engine bundled with
Codex uses different font metrics and reflows both v3 and v4 differently, so its page boundary is not
the authoritative one for this project.

## Primary area and reviewer routing

Select **Interpretability and explainable AI** as the primary area. This is the same primary area used
by PhyloLM and best matches the paper's central question: recovering otherwise hidden lineage and
inherited traits from trained-model weights.

If the submission form permits secondary areas, use this order:

1. **Foundation or frontier models, including LLMs**
2. **Unsupervised, self-supervised, semi-supervised, and supervised representation learning**

MoTHer used the second of these as its primary area, but that reviewer pool may expect a representation
learning or training method. Do not select graph learning merely because the output is a tree; this
paper does not propose a graph-learning method.

Suggested keywords:

- model lineage
- model provenance
- weight-space analysis
- neural phylogeny
- fine-tuning
- large language models

## AI-use disclosure

ICLR 2027 requires an AI-use statement in both the manuscript and submission form. The statement is
present immediately before the references in v4 and records AI assistance with software, numerical
consistency review, manuscript editing, literature work, and citation formatting. It also records author
verification and responsibility for all code, results, claims, figures, and citations.

The policy specifies no penalty for disclosed AI assistance. The authors remain responsible for any
falsehood, plagiarism, or misrepresentation produced with AI assistance. Official policy:
[ICLR 2027 AI Policy for Authors](https://iclr.cc/Conferences/2027/AIPolicyForAuthors).

## Final build

Overleaf is the authoritative build environment. The equivalent local `pdflatex` sequence is:

```bash
cp iclr_draft_v4.tex _b.tex
pdflatex -interaction=nonstopmode _b.tex
bibtex _b
pdflatex -interaction=nonstopmode _b.tex
pdflatex -interaction=nonstopmode _b.tex
```

Before upload, confirm that the rendered header says "Under review as a conference paper at ICLR 2027,"
the main text ends on page 9, references resolve, the PDF contains no author-identifying material, and
the AI-use statement is present.

## Settled paper decisions

- Numerical claims and prose were reconciled against the final tables and workbook.
- Held-out Flan translation is described as a degeneration diagnostic, not held-out competence.
- Behavioral associations are explicitly probe-dependent.
- The headline sparse-weight statement is approximately 3% of Flan's scalar weights in the
  encoder-key subset.
- The paper distinguishes latent-ancestor phylogeny from MoTHer's observed-checkpoint heritage trees.
- PhyloLM is compared on the same known lineages in both the real Mistral family and controlled trees.
- The main text reports one real-checkpoint family; the other real families remain appendix material.
- The scale claim is deliberately narrowed to the architectures and sizes actually tested.

## Pending experiment: restore a round topology sample

Do not start this experiment implicitly. When compute work is explicitly resumed, generate four new
controlled trees whose truths each contain at least one nontrivial unrooted split, then add those same
four topology IDs and locked task/data assignments to **every experiment that uses the fresh shared-tree
controlled grid**. The existing grid generated 50 trees, but IDs 015, 020, 037, and 047 are ineligible
for topology recovery because their truths have no nontrivial split; adding four eligible trees should
produce 54 generated trees and a clean topology-recovery sample of $n=50$.

The scope is literal: propagate the four trees through all Flan and Llama training regimes, weight
subsets/layer diagnostics, direct-lineage and behavioral analyses, and the matched PhyloLM runs. Use
the identical four truth trees wherever a cross-method or cross-regime comparison is claimed. Before
launching training, validate and record that each new truth passes the topology eligibility check.
After all runs finish, recompute the workbook, tables, figures, uncertainty estimates, ranges, sample
accounting, and every manuscript claim derived from the fresh grid. Direct-ordering $n$ should be
reported from its own eligibility rule rather than forced to 50.

Acceptance criterion: every fresh shared-tree condition has 50 topology-eligible observations on the
same truth-tree set, with no condition silently retaining the current $n=46$ subset.

The paper is now in submission-maintenance mode. Reopen substantive writing only for a discovered error,
a genuinely new result, or reviewer feedback.
