# When the “Clean Circuit” Disappears: What I Learned Reverse-Engineering Tool-Calling in GPT-2
Check out the repo at : https://github.com/AymanMahfuz27/tool_call_mech_interp

For the last few days, I’ve been running a mechanistic interpretability project on a question that matters a lot for modern agentic systems:

**How does a language model decide to call a tool vs answer directly?**

If you care about reliability in AI assistants, this decision boundary is everything. A model that calls tools too often wastes latency and money. A model that calls tools too rarely hallucinates on questions that needed execution or retrieval.

I wanted to go beyond behavior-level evals and actually look inside the model.

I fine-tuned GPT-2 Small on a balanced tool/no-tool task, then ran activation patching, direct logit attribution (DLA), SAE feature analysis, and circuit ablations. My first-pass results looked clean. Then I stress-tested my own assumptions, fixed a methodological leak, and the story got much more interesting.

This post is about that full arc: what I found, what broke, what held up, and why I think this matters for frontier labs.

---

## Why This Problem Matters

In production agent systems, tool routing is not a side detail. It is a core control loop.

Every frontier lab that ships assistants with retrieval, calculators, APIs, browsers, or code execution is implicitly betting on stable tool-use decisions under distribution shift. If the routing mechanism is brittle, you get silent failure modes:

- over-calling tools on easy static facts
- under-calling tools on dynamic or computational queries
- unstable behavior after small tuning updates

Behavioral metrics alone tell you **that** it fails. Mechanistic work can help you understand **why**.

---

## The Setup

I trained GPT-2 Small (124M) on a binary decision task:

- Input: user question + available tools list
- Output token: `<|tool_call|>` or `<|no_tool|>`

Dataset:

- 10,000 examples
- exact 50/50 label balance
- explicit balancing of two obvious confounds:
  - number presence
  - dynamic cue words (e.g. “today”, “current”, “latest”)
- 30 minimal-pair counterfactuals

The model reached **99.5% test accuracy**.

That sounds almost solved, but high accuracy is exactly where interpretability can get dangerous: it’s easy to tell yourself a neat mechanistic story that later collapses under causal checks.

---

## The First Mechanistic Signal

Core analyses on the fine-tuned checkpoint:

- Activation patching top head: `L7H6` (score `0.6058`)
- DLA top head: `L11H8` (score `13.4812`)
- Combined ranking proposed a 15-head circuit

At a glance, this looked like a plausible compute→readout split:

- mid/early heads matter causally (patching)
- late heads dominate direct logit writing (DLA)

That pattern is still present after fixes, but wording matters:

I now treat this as **evidence consistent with** a two-stage compute→readout pattern, not a theorem.

---

## The Ablation Result That Changed the Story

I tested the 15-head circuit with sufficiency/necessity ablations:

- Sufficiency (keep only 15 heads, zero 129): **0.50**
- Necessity (ablate those 15, keep 129): **1.00** on a 30-case slice

Because small-slice necessity can be misleading, I ran a larger stress check:

- 400 balanced cases
- Sufficiency: **0.5000** (200/400)
- Necessity: **0.9975** (399/400)

Interpretation:

- the selected 15 heads are **not sufficient**
- they are **near-non-necessary** in this setting

Not strictly non-necessary, because we do see one failure in 400 after ablation. But still close enough that “tiny sparse circuit controls behavior” is not defensible here.

Also important: sufficiency=0.50 is partly a property of very aggressive zero-ablation, not pure mechanism. Zeroing 129 heads is destructive. I call that out explicitly because otherwise the metric invites over-interpretation.

---

## The Strongest Evidence: Circuit-Size Sweep

I then swept circuit size to see when performance recovers:

- 1–30 heads: sufficiency stays at ~0.50, necessity ~1.00
- 60 heads: sufficiency 0.86, necessity 0.55
- 100 heads: sufficiency 0.99, necessity 0.50
- 140 heads: sufficiency 1.00, necessity 0.50

This is the clearest empirical signal in the project:

**Behavior is distributed and redundant.**  
You only recover sufficiency when you retain a large fraction of heads.

---

## The SAE Puzzle (and the Credibility Patch I Needed)

SAE analysis found top features with huge effect sizes (large |Cohen’s d|), but single-feature causal removals were near-zero.

That could mean distributed representation.  
It could also mean my intervention plumbing is wrong.

So I added positive controls using the same hook-based intervention path:

- Ablating known-causal head `L2H1`:
  - mean logit-diff shift: `-0.065`
  - mean absolute per-prompt shift: `0.153`
- Strong control at SAE hook point (`blocks.6.hook_resid_pre`):
  - mean absolute shift: `4.159`

This doesn’t “solve” SAE causality, but it materially improves credibility:
the pipeline **can** move logits when interventions are actually causal/strong.

So near-zero single-feature effects are much more plausibly about representation structure, not just a broken hook.

---

## Robustness Across Seeds (At Least One Extra Check)

I added an extra seed robustness run for patching pairs:

- seed 42 vs seed 123, 20 pairs each
- Spearman rank correlation across all 144 heads: **0.995**
- Top-10 overlap: **10/10**, Top-20 overlap: **20/20**
- Top head swapped (`L2H1` vs `L7H6`)

Takeaway:

- local “winner” head can swap
- global ranking structure stayed very stable in this check

This is not full multi-seed training replication yet, but it’s better than a pure single-seed narrative.

---

## Behavioral Probing: What the Model Still Gets Wrong

Adversarial probes remained revealing:

- numbers + no tool needed: **73.3%**
- no-number-keyword + tool needed: **100%**
- computation-adjacent definitional prompts: **100%**

So pure keyword matching is unlikely, but richer surface heuristics are still plausible.
The model seems to over-trigger tool calls on some numeric factual questions (“specific quantity” as proxy for “needs lookup/compute”).

---

## A Methodological Miss I Had to Fix

One important correction during this pass:

I found and fixed label-token leakage risk in analysis prompts.  
I now enforce unlabeled inference prompts (`...Assistant:`) and reject prompts containing decision tokens in DLA/SAE/ablation paths.

I also added tests to prevent regression.

This was a good reminder that in mech interp, small bookkeeping mistakes can create big interpretive illusions.

---

## What This Means for Frontier Labs

If you work on tool-use reliability at scale (Anthropic/OpenAI/DeepMind/etc.), I think there are three practical implications:

1. **Don’t trust clean sparse-circuit stories by default.**  
   Confound control and ablation stress tests can turn “clean circuit” into “distributed redundancy” quickly.

2. **Require causal positive controls, not just correlational feature rankings.**  
   Big effect sizes in SAE space are not equivalent to causal leverage.

3. **Treat routing as a systems problem, not a single-metric problem.**  
   You need behavior evals + mechanism evals + robustness evals together.

The main engineering lesson here is one I care about a lot:
**claim discipline beats pretty plots**.

---

## What I’d Do Next

If I extended this with more compute/time, top priorities would be:

- multi-seed checkpoint replication (>=3 full runs)
- mean-ablation and path-specific interventions (less destructive than zero-ablation)
- multi-feature/subspace SAE interventions
- MLP-inclusive circuit analysis (not heads only)
- template-holdout and paraphrase-holdout generalization

I also want to run this on larger and more realistic tool-calling setups, where the decision boundary is noisier and closer to production behavior.

---

## Closing

I started this project hoping to find a neat, compact “tool-call circuit.”  
Instead, after tighter controls, I found something messier and probably more realistic: a redundant distributed mechanism with stable global structure and unstable local leaders.

I’m happy with that outcome.

Not because it’s cleaner.  
Because it’s harder to fake.

---

### Repro Artifacts

Main outputs:

- `results/full_results.json`
- `results/deep_analysis_results.json`
- `results/ablation_stress_results.json`
- `results/circuit_size_sweep.json`
- `results/causal_positive_control.json`
- `results/patching_seed_robustness.json`

Core scripts:

- `run_pipeline.py`
- `scripts/deep_analysis.py`
- `scripts/causal_positive_control.py`
- `scripts/patching_seed_robustness.py`
