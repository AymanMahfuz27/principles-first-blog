# Testing a proposed tool-calling circuit in GPT-2

I fine-tuned GPT-2 Small to choose whether to call a tool, then investigated which attention heads contributed to that choice. The model reached 99.5% test accuracy, but the 15-head circuit proposed by my initial rankings did not retain the behavior under ablation. The clearest result was that performance recovered only when I kept a much larger fraction of the heads.

The [repository](https://github.com/AymanMahfuz27/tool_call_mech_interp) contains the code and results. This is a controlled binary routing task in a small model. It lets me test an interpretability workflow, but it does not reproduce the complexity of tool use in a deployed assistant.

## The task and initial evidence

The model was GPT-2 Small with 124M parameters. Each input contained a user question and a list of available tools; the output was either `<|tool_call|>` or `<|no_tool|>`. The dataset had 10,000 examples with an exact 50/50 label balance. I also balanced number presence and dynamic cue words such as “today,” “current,” and “latest,” and included 30 minimal-pair counterfactuals.

Those controls mattered because a model could achieve high accuracy through a shortcut. A number can appear in a factual question that needs no computation, and a tool-requiring question may contain no obvious dynamic keyword. Balancing those cues made the simplest explanation less plausible without ruling out every surface heuristic.

Activation patching ranked `L7H6` highest, with a score of `0.6058`. Direct logit attribution, or DLA, ranked `L11H8` highest, with a score of `13.4812`. Combining the rankings produced a proposed 15-head circuit. The difference between the patching and DLA rankings was consistent with earlier computation feeding a later readout, but it did not establish that two-stage mechanism.

Patching and attribution answer different questions. An intervention can show that changing an activation affects the output under a particular setup. DLA measures a component's direct contribution to the logits. A component can rank highly by one measure without being sufficient for the complete behavior.

## What happened when I ablated the circuit

I tested sufficiency by keeping the selected 15 heads and zeroing the other 129. I tested necessity by zeroing the selected heads and keeping the others. On the initial 30-case slice, accuracy was 0.50 in the sufficiency condition and 1.00 in the necessity condition. I then expanded the check to 400 balanced cases.

| Intervention | Accuracy | Correct cases |
|---|---:|---:|
| Keep the selected 15 heads; zero the other 129 | 0.5000 | 200/400 |
| Zero the selected 15 heads; keep the other 129 | 0.9975 | 399/400 |

The selected heads were not sufficient under this intervention, and removing them caused very little accuracy loss. The one failure after removal prevents a claim of strict non-necessity on that sample. More broadly, the result did not support my initial interpretation that a compact set of highly ranked heads controlled the task.

Zeroing 129 heads is also a destructive intervention. Chance accuracy in that condition does not independently identify the natural computation the model uses. It tells me that this proposed circuit does not preserve the behavior when most of the surrounding attention computation is removed.

## How much of the model had to remain

The circuit-size sweep made the pattern clearer. The columns below report accuracy after keeping or removing the ranked set of heads.

| Ranked heads selected | Keep selected heads | Remove selected heads |
|---|---:|---:|
| 1–30 | ~0.50 | ~1.00 |
| 60 | 0.86 | 0.55 |
| 100 | 0.99 | 0.50 |
| 140 | 1.00 | 0.50 |

Under these ablations, a large retained set recovered the task while small retained sets did not. The results are consistent with distributed and redundant contributions. They are not a complete circuit description: the sweep is head-focused, excludes a full account of MLP computation, and depends on the ranking and ablation method.

## Checking the SAE intervention path

Sparse autoencoder analysis identified features with large absolute Cohen's d values between classes. Removing individual features had near-zero causal effects. That could reflect a distributed representation, but a broken intervention path could produce the same apparent result.

I added positive controls through the same hook-based path. Ablating the known-causal head `L2H1` produced a mean logit-difference shift of `-0.065` and a mean absolute per-prompt shift of `0.153`. A strong intervention at `blocks.6.hook_resid_pre`, the SAE hook point, produced a mean absolute shift of `4.159`. The pipeline could move the logits when given an intervention with a measurable effect.

Those controls reduced the likelihood of a completely ineffective hook. They did not establish that every SAE feature intervention was adequate or that the near-zero effects had a single explanation. Multi-feature and subspace interventions would be needed to investigate whether the relevant signal is distributed across features.

## Robustness and remaining shortcuts

I repeated the patching-pair analysis with seeds 42 and 123, using 20 pairs each. Across all 144 heads, the Spearman rank correlation was 0.995. The top-10 overlap was 10/10 and the top-20 overlap was 20/20, although the highest-ranked head changed between `L2H1` and `L7H6`.

The broad ranking was stable in that check while the local winner changed. These were different patching-pair seeds, not independent training replications. The result supports stability of that analysis on the checkpoint; it does not establish robustness across separately trained models.

Behavioral probes also exposed a remaining weakness. Accuracy was 73.3% on numeric questions that did not need a tool, 100% on tool-requiring questions without the number keyword, and 100% on computation-adjacent definitional prompts. A simple keyword account did not explain the whole pattern, but more elaborate surface heuristics remained plausible. Some numeric factual questions still triggered tools unnecessarily.

## Fixing an analysis leak

During the analysis, I found a risk of label-token leakage in the prompts. I changed the inference paths to use unlabeled prompts ending in `...Assistant:` and reject prompts containing decision tokens in the DLA, SAE, and ablation paths. I added tests to prevent the same mistake from returning.

This was an important correction because an analysis prompt can make the apparent mechanism easier than the original task. Confound controls in the dataset do not protect against a label introduced later by the evaluation code. The analysis pipeline has to preserve the problem we intended to investigate.

The next checks I would prioritize are at least 3 independently trained checkpoints, mean ablation and path-specific interventions, multi-feature SAE interventions, MLP-inclusive analysis, and template or paraphrase holdouts. Larger models and realistic tool schemas would then test whether the conclusions transfer beyond this binary setup.

The project changed the standard I use for a circuit claim. High behavioral accuracy and a plausible ranking are the beginning of the investigation. The ablations, positive controls, and leakage checks determine how much of the explanation survives.

## Reproduction artifacts

The main result files are:

- `results/full_results.json`
- `results/deep_analysis_results.json`
- `results/ablation_stress_results.json`
- `results/circuit_size_sweep.json`
- `results/causal_positive_control.json`
- `results/patching_seed_robustness.json`

The core scripts are:

- `run_pipeline.py`
- `scripts/deep_analysis.py`
- `scripts/causal_positive_control.py`
- `scripts/patching_seed_robustness.py`
