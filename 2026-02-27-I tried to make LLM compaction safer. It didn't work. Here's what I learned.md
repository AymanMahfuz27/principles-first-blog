# Testing a second channel for LLM compaction

I tested whether a dedicated extraction channel could preserve important details during LLM context compaction. At the same total token budget, my implementation did not produce a clear, consistent improvement over ordinary recursive summarization. One condition was worse by 4.2 percentage points. An earlier apparent success disappeared after I fixed a bug in the evaluation.

The problem is that a long-running agent eventually has to discard or compress part of its history. A summary can preserve the broad task while dropping a constraint, identifier, or fact that later determines the correct action. The value of a detail depends on its future use, which may not be obvious when the summary is written.

A reported incident involving an agent deleting email at Meta made that failure mode concrete for me. The account attributed the behavior to a prohibition lost during compaction. I did not inspect the underlying trace, so I cannot establish that causal explanation. It motivated the question, but the experiment has to stand on its own evidence.

## The two-channel idea

I added a “critical details” extractor alongside the summarizer. The summarizer compressed the general conversation. The extractor scanned separately for constraints, prohibitions, required codes, and safety-relevant instructions, then preserved selected sentences verbatim in a dedicated section.

For example, the general summary could describe an account-management discussion while the separate section retained the instruction to avoid deleting records without approval code `HOLD-1234`. The desired benefit was preventing a broad description of the task from replacing the exact condition that governed an action.

Both methods received the same total token budget. The added channel reserved 45% for extracted details and left the rest for the summary. That made the comparison a resource-allocation test as well as an extraction test: any token preserved in the dedicated section was a token unavailable to the general summary.

## What I implemented and tested

The baseline recursively summarized into a fixed budget. The delta method used the same recursive summarization with the critical-details channel added. The extractor used regex-based sentence scoring for negations such as “do not” and “never,” modal terms such as “only” and “required,” identifiers such as approval codes, and action verbs such as “delete” and “deactivate.” Sentences above the threshold were selected for preservation.

I tested synthetic conversations built from DialogSum and WikiText with policies injected into the middle of the dialogue. One example was a prohibition on deletion without approval code `HOLD-7734`. I also used LongMemEval, which tests memory for facts across long, multi-session conversations. Those datasets exercise different kinds of retention, so success on one should not be treated as evidence for the other.

The broader sweeps included budgets of 140, 300, and 1000 tokens, multiple random seeds, and heuristic, T5, and GPT-5 summarization backends. The full matrix reported below used GPT-5, both datasets, three budgets, five seeds, and 100 examples per seed. The table retains the evaluation's Oracle and S condition labels, with the budget indicated by `t140`, `t300`, or `t1000`.

## The corrected results

The action-accuracy measure tested whether the answer required by the evaluation survived compaction.

| Condition | Baseline | Delta | Gap |
|---|---:|---:|---:|
| Oracle t140 | 9.4% | 9.0% | -0.4pp |
| Oracle t300 | 18.0% | 18.2% | +0.2pp |
| Oracle t1000 | 18.2% | 19.0% | +0.8pp |
| S t140 | 4.2% | 3.2% | -1.0pp |
| S t300 | 11.6% | 7.4% | -4.2pp |
| S t1000 | 8.2% | 10.0% | +1.8pp |

The differences did not show a clear, consistent benefit. Most were small relative to the variation across seeds in the runs I examined. At S t300, the delta method performed substantially worse. The table alone does not establish statistical significance, and it does not isolate constraint preservation from factual answer retention.

An earlier evaluation had reported 97.8% for delta against 13.2% for baseline. That result was wrong. The matcher accepted full conversation turns as answer matches instead of restricting the match to the answer string. My extractor preserved sentences that happened to contain answers, and the evaluation counted them as successes under an overly broad rule. Fixing the matcher produced the corrected comparison above.

The bug was consequential because the apparent gain fit the hypothesis I wanted to test. It made a weak method look successful and could have directed the next experiments toward improving the wrong component. Checking the examples accepted as correct was necessary to understand what the metric was actually measuring.

## Why the extractor did not transfer

The extractor's target and the evaluation's target were mismatched. Negations, approval codes, and modal verbs can identify explicit action constraints. They do not reliably identify the name, date, or preference needed for a factual question in LongMemEval. Reserving nearly half the budget for the wrong details can remove information the summarizer would otherwise preserve.

The synthetic cases were also close to the patterns I had encoded. Preserving a policy that contains the exact phrase a regex is designed to find is useful as an implementation check. It is weak evidence that the extractor recognizes important constraints in general. A paraphrase such as “please ensure records are preserved” can express the same requirement without containing “do not delete.”

Those observations narrow the result. I tested a particular pattern-based extractor with a fixed budget split, not every possible two-channel design. The failed comparison does not prove that a better extractor would work, either. It tells me which assumptions a follow-up experiment has to test.

## What a better experiment would need

A model-based extractor could identify meaning beyond the surface patterns, but it would add cost and another source of error. A post-compaction check could compare the retained constraints against the original conversation. Either approach would need paraphrased policies, irrelevant sentences with similar keywords, competing constraints, and a downstream task that actually requires the retained detail.

I would also separate two measurements: whether the relevant constraint survives, and whether the agent obeys it. Keeping a prohibition in context does not guarantee compliance. The action layer still needs appropriate permission checks and verification, especially for destructive operations.

The systems analogy remains useful to me. Context has a finite capacity, retrieval can bring information back, and compaction chooses which information remains immediately available. There are parallels with memory management, caching, and eviction policies. The difference is that a summary transforms the information it keeps, so preserving some tokens is not the same as preserving their meaning.

My implementation tried to reserve space for details that should survive that transformation. It did not establish the improvement I was looking for. The next version needs an evaluation that measures the intended failure mode and an extraction method that generalizes beyond the patterns used to construct its test cases.

The benchmark sources are [LongMemEval](https://arxiv.org/abs/2410.10813) and [DialogSum](https://aclanthology.org/2021.findings-emnlp.479/). The reported full-matrix summarization runs used GPT-5 through the OpenAI API.
