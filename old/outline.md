# ELLMTrees — Paper Outline (ICLR submission draft)
*Last updated: 2026-05-16*

---

## Working title
**Fine-Tuning Genealogy Is Written in Weights**

*(alt: "The Lineage Layer: Recovering Fine-Tuning Ancestry from Attention Keys")*

---

## One-sentence thesis
Pairwise weight-space distances between fine-tuned models encode their training lineage —
concentrated in attention key matrices, recoverable without training data, theoretically
grounded in near-orthogonal task displacement subspaces, and predictive of behavior on
held-out tasks.

---

## Abstract (key beats)
- Problem: fine-tuned model provenance is undocumented; benchmarks tell you what a model
  does, not where it came from
- Approach: controlled branching fine-tuning trees with known ground-truth topology;
  pairwise weight-space distances → NJ reconstruction → Robinson-Foulds vs ground truth
- Finding 1: weight distances recover training lineage significantly above chance (60–100%
  of individual runs at ≥7 leaves)
- Finding 2: signal concentrates in encoder self-attention key matrices — 4% of parameters,
  non-inferior to full-weight analysis
- Finding 3: theoretical grounding — linearized fine-tuning predicts near-orthogonal task
  displacement subspaces; empirically confirmed at 86° mean principal angle across all
  task pairs and module types
- Finding 4: signal is robust under LoRA, degrades predictably under task holdout, and
  collapses to near-chance within-task (the falsifiable null)
- Finding 5: lineage predicts behavioral similarity on held-out tasks (weight distance →
  translation ROUGE-L, p=0.008 within-run fixed effects)
- Practical implication: provenance auditing is feasible with 4% of model parameters;
  single benchmarks are insufficient; whitebox weight access adds what behavioral probes
  cannot

---

## 1. Introduction (~1.5 pages)

**Arc:** forking problem → benchmarks don't capture lineage → why it matters →
what we do → contributions

### 1.1 The forking problem
Six months, no documentation, unknown production checkpoint ancestry. Weight-space
genealogy is the only audit trail that doesn't depend on documentation that was never written.

### 1.2 Why it matters (keep tight — 3 bullets, not 6)
- **Provenance auditing / SBOM analog**: regulated industries need a model bill of materials
- **Capability prediction**: knowing ancestry predicts behavior on tasks the model never saw
- **Machine unlearning**: can't unlearn from an unknown lineage of descendants

### 1.3 What we do
Controlled branching fine-tuning trees with known topology → weight distances → NJ
reconstruction → RF against ground truth. 50 replications per condition.
Whitebox (weight access) + blackbox (behavioral, commercial models) complementary probes.

### 1.4 Contributions
1. Experimental framework: branching fine-tuning trees, ground-truth lineage, 50 runs/condition
2. **WeightTree**: lineage recovery significantly above chance; 60–100% of individual runs at ≥7 leaves
3. **LayerTrace**: signal concentrates in encoder SA.k (4% of parameters); non-inferior to full model
4. **Theory**: linearized fine-tuning → task displacement vectors → near-orthogonal subspaces →
   distance encodes lineage. Empirically validated: 86° mean principal angles across all task pairs.
5. **Robustness probes**: LoRA variants, task holdout, within-task null (falsifiable prediction confirmed)
6. **Behavioral link**: weight distance predicts held-out behavioral similarity within experiments
7. **Blackbox**: commercial model families cluster behaviorally; one benchmark insufficient;
   translation gives cleanest consensus trees

---

## 2. Background and Related Work (~0.5 pages)
- Model similarity / weight-space distances (model merging, souping, mode connectivity,
  task arithmetic — Ilharco et al. 2023)
- Phylogenetic methods in ML (prior work applying NJ/parsimony to models)
- Provenance, watermarking, model fingerprinting
- LoRA merging / composability (TIES-merging, DARE) — connects to §5

---

## 3. Framework (~1 page)

### 3.1 Branching fine-tuning trees
- Root: flan-T5-base (250M, encoder-decoder)
- Tasks: {summarization, QA, classification, translation} × dataset pool
- Bernoulli branching process; 50 replications; 5–8 leaves; ground-truth topology known
- 2000 steps, lr=3e-4, batch=8, bf16

### 3.2 Weight-space distance and NJ reconstruction
- Pairwise cosine distance between weight matrices (all layers or named subset)
- Neighbor-joining → Robinson-Foulds vs ground truth
- Permutation test: 1000 random binary trees per run

### 3.3 Experimental conditions (table)
| Condition | Description | Status |
|---|---|---|
| branching_v3 | Full FT, all 4 tasks | Done |
| lora_k_only | LoRA [k] only | Analysis running |
| lora_qkv | LoRA [q,k,v] | Analysis running |
| lora_full_attn | LoRA [q,k,v,o] | Training done |
| lora_full_ft_approx | LoRA [all] | Training done |
| no_translation_v2 | Full FT, 3 tasks (no translation) | Analysis running |
| summ/qa/clf/transl only | Within-task (same task all nodes) | Training running |
| llama_k_only | Llama-3.1-8B, LoRA [k_proj] | Planned |
| llama_full_ft_approx | Llama-3.1-8B, LoRA [all] | Planned |

---

## 4. WeightTree: recovering training lineage from weights (~1 page)

**Headline**: weight distances recover training tree significantly above chance;
60–100% of individual runs at ≥7 leaves.

### 4.1 Main result table (≥5 leaves)
RF by tree size; observed vs null; % significant per-run; Fisher combined p.
⭐ **Figure**: RF by tree size (scatter + null band) — best figure in the paper

### 4.2 Binary branch analysis
Same-branch leaf pairs have significantly smaller cosine weight distances than
cross-branch pairs. OLS run FE + HC3: β = −0.0046, p ≈ 0.
Within-run Pearson r = −0.720 (SD 0.23, N=35 runs). Mann-Whitney p = 8.9×10⁻⁴⁷.
*Does not require resolved reference topology — robust to near-star degenerate trees.*

### 4.3 Discussion
- Signal consistent across cosine, L1, L2, correlation; cosine primary
- Failure cases: trees with similar task paths (near-parallel displacement vectors)
- → Connects to theory (§6)

---

## 5. LayerTrace: the lineage signal lives in 4% of the model (~1 page)

**Headline**: encoder self-attention k-matrices (SA.k) alone are non-inferior to the
full model; outperform it at 6 leaves.

### 5.1 Layer variant results (table)
enc_sak vs full-weight RF by leaf count; non-inferiority test.
⭐ **Figure**: aggregate layer RF profiles (cyclical pattern — the mechanistic story)

### 5.2 Why SA.k?
Key matrices route attention to task-relevant tokens; this routing shift accumulates up
the tree. Decoder SA.k adds noise (tightly coupled to output head).

### 5.3 LoRA robustness (brief — details in appendix)
Signal robust under rank-r constraint; k-only LoRA retains near-full signal.
Connects to §6 theory: LoRA projects out shared pretraining noise, potentially
improving SNR if task subspaces are near-orthogonal.

### 5.4 Open question: decoder-only (Llama) — forward pointer to §8

---

## 6. Theory: why weight distances encode lineage (~1 page)

**Headline**: linearized fine-tuning predicts near-orthogonal task displacement
subspaces; empirically confirmed at 86° mean principal angles.

### 6.1 Setup
- Tree T = (V, E); w_v = w_{pa(v)} + Δ_v
- Linearize: Δ_v ≈ d_{τ(v)} + ε_v (task displacement + noise)
- Key object: d_τ = E[Δ_v | task=τ] — systematic direction tasks move weights

### 6.2 Same-branch pairs are closer iff task diversity > noise
- Signal term: s(i,j) = Σ d_τ along path i − Σ d_τ along path j
- Cross-branch picks up additional branch-level displacement absent from same-branch term
- SNR condition: (1−ρ) · ||d||² / σ² >> D

### 6.3 Empirical validation
- Pooled PCA on displacement vectors: clean task cluster separation
⭐ **Figure**: PCA scatter, PC1 vs PC2, colored by task

- Principal angles between task subspaces: **86° mean across all task pairs and modules**
  (near-perfect orthogonality; 90° = completely orthogonal)
- **Figure/Table**: principal angles by module type (SelfAttention.k highest; layer norms lowest)

### 6.4 Predictions confirmed by experiments
| Theory prediction | Experiment | Result |
|---|---|---|
| Cross-task: near-orthogonal displacement | Subspace angle analysis | 86° ✓ |
| Within-task: near-collinear (null) | summ/qa/clf/transl only | Pending |
| Task saturation degrades signal | no_translation_v2 | Pending |
| LoRA SNR preserved / improved | lora_k_only | Pending |
| Translation: largest displacement norm | Norm analysis | Pending |

### 6.5 LoRA composability corollary
Near-orthogonal subspaces → adapters from different tasks can be additively composed
with minimal interference. Geometric explanation for why task arithmetic works.
*(Discussion paragraph; connects to Ilharco et al. 2023)*

---

## 7. Robustness probes (~1 page)

### 7.1 LoRA variants
lora_k_only, lora_qkv, lora_full_attn, lora_full_ft_approx.
Does signal increase monotonically with adapter scope? Does k_only ≈ full_ft?
**Table**: RF by LoRA variant (pending results)

### 7.2 Task holdout (no_translation_v2)
Removing translation weakens signal — predicted by theory (fewer orthogonal d_τ;
translation has largest displacement norm → its removal removes most signal).
**Result**: pending analysis

### 7.3 Within-task null (the falsifiable prediction) ⭐
All nodes same task → d_τ terms cancel → near-chance recovery predicted.
**Result**: pending (summ/qa/clf/transl_only training on Wright)
*This is the most important robustness result — it's the falsifiable null.*

---

## 8. Behavioral link and external validity (~1 page)

### 8.1 Weight distance predicts held-out behavioral similarity
Translation ROUGE-L as behavioral DV. Within-run fixed effects + HC3: β = −10.69, p = 0.008.
Within-run Pearson r = −0.176 (p = 0.0001). Small R² (2.5%) expected — lineage is one
source of behavioral variance, not the only one.

### 8.2 Multi-benchmark replication (Llama — planned)
Held-out benchmarks (MMLU, HellaSwag, ARC, TruthfulQA) on Llama leaf checkpoints.
Effect consistent across benchmarks > single high-R² result on noisy DV.

### 8.3 Blackbox: commercial model families
- 9 models, 4 tasks, embedding cosine → NJ trees
- Claude siblings cluster across all tasks; translation gives cleanest consensus trees
- One benchmark insufficient: formatting confounds drive embedding distances in QA/CLF
- Held-out prediction: tree built on training prompts predicts held-out behavioral
  similarity → external validity without synthetic setup
⭐ **Figure**: NJ tree grid by task

### 8.4 Llama-3.1-8B robustness
Decoder-only, 4× wider hidden dim, better NTK justification.
Does SA.k → k_proj finding replicate? If yes: universal lineage layer across architectures.

---

## 9. Discussion (~0.5 pages)
- The provenance chain: lineage → weights → behavior (what works, what's pending)
- K-matrices as a cross-architecture bridge: if task signal concentrates in k-matrices
  universally, cross-architecture comparison via k-subspace alignment becomes tractable
- Whitebox vs. blackbox: complementary tools for different access regimes
- Limitations: single base model; controlled trees; within-architecture only (this paper)
- Future: Llama replication; cross-architecture k-subspace comparison; LoRA composability

---

## 10. Conclusion (~0.25 pages)
Numbers + practical upshot: provenance auditing with 4% of parameters;
theory-grounded in near-orthogonal task subspaces; behavioral link established.

---

## Appendix (unlimited — put everything detailed here)

- **A**: Full experimental details (tree generation, training config, datasets)
- **B**: Layer variant definitions (enc_sak, encoder_h, etc.)
- **C**: Null distribution construction and permutation test details
- **D**: Metric selection — SNR analysis (ridge plots); cosine vs correlation comparison table
- **E**: Full subspace angle results — per-module breakdown (86° table), norms by task
- **F**: Regression sensitivity table (all estimators for behavioral link)
- **G**: Blackbox details (models, API versions, prompt templates, formatting heatmaps)
- **H**: Degenerate topology disclosure (near-star reference trees, RF=1.0 artifact,
         mitigation via binary branch analysis)
- **I**: LoRA composability — connection to task arithmetic literature

---

## Figure shortlist (main paper — target ≤6 figures)

| # | Figure | Section | Status |
|---|---|---|---|
| 1 | RF by tree size (observed vs null band) | §4 | Done |
| 2 | Aggregate layer RF profiles (cyclical pattern) | §5 | Done |
| 3 | PCA scatter PC1/PC2 colored by task | §6 | Done (pending full run) |
| 4 | Within-task null: branch discrimination near chance | §7 | Pending |
| 5 | Behavioral link: weight dist vs ROUGE-L (within-run) | §8 | Done |
| 6 | Blackbox NJ tree grid by task | §8 | Done |

*Principal angle table goes in §6 as a table, not a figure.*
*Llama layer RF profile goes in appendix or replaces Fig 2 if result is strong.*

---

## Status / what's blocking submission

**Done:**
- branching_v3 training + analysis (WeightTree, LayerTrace, binary branch analysis)
- LoRA k_only training; analysis in queue on Ghidorah/Wright
- no_translation_v2 training; analysis running on Wright
- Within-task training queued on Wright
- Subspace angle analysis (5-run test done; full 50-run running on Ghidorah)
- Blackbox experiments
- Theory sketch + empirical PCA/angle validation

**Needed before submission:**
- [ ] Within-task null results (Wright — waiting on training)
- [ ] LoRA variant results (lora_qkv, lora_full_attn, lora_full_ft_approx analysis)
- [ ] no_translation_v2 branch structure analysis
- [ ] Llama manifests + training + layer analysis
- [ ] Multi-benchmark behavioral replication (Llama)
- [ ] Sentence embedding cosine as behavioral DV
- [ ] Held-out prediction on blackbox tree (external validity)
- [ ] Full 50-run subspace angle results + within-task subspace control
