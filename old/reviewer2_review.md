# Reviewer 2 Style Review of “The Traits are in the Weights”

## Overall Assessment

This is a strong and timely paper with a clean central claim: fine-tuning lineage leaves measurable structure in weight space, and standard phylogenetic tools can recover much of the observed leaf topology. The most compelling result is the controlled whitebox experiment: across architectures and fine-tuning regimes, the method recovers a high fraction of true clades, and in the authors’ updated framing approximately 82% of trees recover the true topology. That is the result I would build the paper around.

My main recommendation is to narrow the claims to what the experiments most directly establish. The paper should say that it recovers the topology among observed leaf checkpoints, not the full directed training tree. It should also foreground recovery percent / FN / exact-topology recovery over per-run significance against the label-permutation null, since the low-power 5-leaf cases appear to drive an artificial under-reporting of success. With those changes, the paper becomes much harder to object to.

## Summary

The paper studies whether fine-tuned language models can be treated as leaves of a phylogeny, with pairwise distances in weight space used to recover the training lineage. The authors evaluate this in controlled synthetic training trees with known topology, across encoder-decoder and decoder-only models, full fine-tuning and LoRA, and several model scales. They further analyze which layers carry lineage signal, connect weight-space proximity to behavioral similarity, and include an exploratory blackbox behavior-only analysis over frontier and open models.

The strongest contribution is the controlled recovery of leaf topology from weights. The layer localization result, especially the attention-key signal, is also interesting and gives the paper more than just an application of neighbor joining to model checkpoints.

## Strengths

- The controlled tree-generation setup provides real ground truth, which is rare in model-lineage work.
- The paper distinguishes direct pairwise ordering from harder topology recovery.
- The recovery results appear robust across several architectures, scales, and adaptation regimes.
- The layer-wise analysis is a genuinely useful diagnostic and could be important for future model-provenance tools.
- The use of Atteson-style additivity and a label-free quartet proxy gives the method a principled statistical frame rather than just a heuristic clustering story.

## Major Comments

### 1. “Full training tree” overstates what is recovered

The current draft repeatedly suggests that the full training tree is recovered. However, the method observes only leaf models and estimates an undirected phylogenetic topology with latent internal nodes. It does not recover the directed parent-child training graph, the root-to-leaf chronology, or the identity of intermediate checkpoints unless those checkpoints are included as observed leaves.

I recommend replacing “full training tree” with language such as:

- “observed leaf topology”
- “clade structure among observed checkpoints”
- “the topology induced by the training tree over observed leaves”

This is not merely cosmetic. A reviewer will otherwise object that the paper claims to recover more than the algorithm is capable of identifying.

### 2. Emphasize recovery percent, not per-run significance

The current tables include a “% significant” column against the label-permutation null, and the text notes relatively low aggregate significance rates. If the authors’ updated analysis shows that about 82% of trees recover the true topology, while the 5-leaf cases lack enough permutation power, then the current significance framing is underselling the main result and inviting the wrong critique.

I would make exact topology recovery, clade recovery, and FN the primary metrics. The label-permutation null can remain as a secondary sanity check, but it should be explicitly described as underpowered for small trees. If possible, report it stratified by leaf count or move it to the appendix.

Suggested framing:

> Across controlled trees, the method exactly recovers the induced observed-leaf topology in 82% of runs and recovers most true clades in the remaining cases. The label-permutation test is conservative and has little power at 5 leaves, so we treat it as a secondary check rather than the primary recovery metric.

### 3. Clarify the task/dataset confound baseline

When I suggested a “task-label baseline,” I did not mean the leaf labels/model names. I meant a metadata-only baseline constructed from the known task or dataset assignments in the controlled experiment.

The concern is: if models trained on similar task paths cluster together, some apparent lineage recovery might be driven by task identity rather than ancestry. A reviewer may ask whether the method recovers genealogy or merely groups translation-trained models with translation-trained models, classification with classification, etc.

Possible baselines:

- Last-task baseline: define pairwise distance as 0 if two leaves share the same most recent task, 1 otherwise.
- Task-path baseline: compare leaves by the set or sequence of task types along their root-to-leaf paths.
- Dataset baseline: same idea, but using dataset identity rather than task family.
- Oracle metadata baseline: build the best possible tree using only task/dataset metadata, then show it recovers less topology than weight distances.

This baseline is not meant as a deployable method. It is a controlled-experiment sanity check showing that weights contain ancestry signal beyond obvious task metadata.

### 4. The behavior bridge should be framed as supportive, not central

If the newer Llama probes are seen tasks and all show significant negative correlations, that strengthens the weight-to-behavior story. I would still avoid making it carry the paper. The behavior results are best presented as evidence that weight-space lineage is behaviorally meaningful, not as proof that behavioral outputs alone recover lineage.

Recommended framing:

> Weight-space proximity predicts behavioral similarity across multiple probes, including held-out probes in some settings and additional seen-task probes in Llama. These effects support the interpretation that weight-space lineage captures behaviorally relevant variation, although behavior-only topology recovery remains substantially noisier than weight-based recovery.

### 5. Behavior-only frontier phylogeny should be exploratory

The blackbox frontier-model result is interesting, but without ground truth and with high task dependence, it should not be sold as a coequal contribution. I would call it an exploratory extension or demonstration. The paper’s core should remain the controlled whitebox recovery result.

### 6. Clarify LoRA distance definitions

The draft should make the LoRA comparison unambiguous. In different places it refers to merged weights, trained-only tensors, and adapter scopes. A reader needs one clean description of exactly what distance is computed for each LoRA condition.

At minimum, define:

- whether distances use merged full weights, adapter deltas, trained tensors only, or all tensors;
- whether this differs between Flan and Llama;
- why this is the fairest comparison to full fine-tuning.

If space allows, a small ablation comparing merged weights vs adapter-only deltas would remove a likely reviewer objection.

## Recommendation

I would currently rate this as a promising paper whose main result is strong but whose claims need tightening. If revised to emphasize observed-leaf topology recovery, exact recovery percent, and the controlled whitebox setting, I would view it as a solid ICLR submission.

The paper should resist trying to be three papers at once. The best version is:

1. Weight distances recover observed fine-tuning topology.
2. The signal is localized and diagnosable by layer.
3. The recovered weight-space structure is behaviorally meaningful.
4. Behavior-only lineage is possible but noisy and exploratory.

That version is cleaner, more defensible, and much less vulnerable to reviewer objections.
