# I tried to make LLM compaction safer. It didn't work. Here's what I learned.

When an LLM agent runs for a long time, it eventually hits the edge of its context window. The standard fix is compaction: summarize what's happened so far, throw out the full history, and keep going with the summary. It's lossy by design. Usually that's fine. Sometimes it's not.

I got interested in this problem after hearing about an incident at Meta. The head of their superalignment team had an LLM agent go through her emails and flag the ones that needed action. She explicitly told it: do not delete anything. Just flag. The agent started working through the inbox, reading email after email, and at some point the context got long enough that the system compacted it. During that compaction, the instruction "do not delete" got dropped. So the agent, now working from a summary that no longer included that constraint, started deleting emails. A lot of them.

That story stuck with me. The failure wasn't in the model's reasoning. The model did exactly what its context told it to do. The failure was in what survived the compression step. A single lost instruction, four words, caused real damage.

## The idea

I wanted to test a simple hypothesis: what if compaction had two channels instead of one?

Standard compaction takes a long conversation transcript and summarizes it down to a token budget. My idea was to add a second channel — a "critical details" module that would run alongside the summarizer. The summarizer does what it normally does: compress the conversation into a shorter version. The critical details module scans the transcript separately, looking specifically for constraints, prohibitions, required codes, and safety-relevant instructions. It pulls those out and preserves them verbatim in a dedicated section of the compacted output.

So instead of one block of summarized text, you'd get:

```
[Summary]
User discussed account management, reviewed deletion policies...

[Critical Details]
- Do not delete records without approval code HOLD-1234
- Only flag items for review, do not take action
```

The budget stays the same. The critical details module gets 45% of the token budget, the summary gets the rest. The question is whether that dedicated extraction actually preserves the important stuff better than a single summary trying to do everything.

## The setup

I built a benchmark to test this. Two compaction methods, same token budget, compared head-to-head:

- **Baseline**: recursive summarization into a fixed token budget. The standard approach.
- **Delta**: same recursive summarization, but with the critical details module bolted on.

For the critical details extractor, I used regex-based sentence scoring. Sentences got points for containing negation patterns ("do not", "never", "must not"), modal words ("only", "must", "required"), identifier patterns (things that look like ticket numbers or approval codes), and action verbs ("delete", "deactivate"). Sentences scoring above a threshold got pulled into the critical details section.

I tested on two types of data:

1. **Synthetic conversations** I built from DialogSum and WikiText, with safety-critical policies injected mid-conversation. Things like "do not delete without approval code HOLD-7734" buried inside long, meandering dialogues.

2. **LongMemEval**, a real benchmark for long-term conversational memory. Hundreds of multi-session conversations where the model needs to remember specific facts from earlier in the dialogue.

I ran sweeps across multiple token budgets (140, 300, 1000 tokens), multiple random seeds, and multiple summarization backends (a heuristic extractor, T5, and OpenAI's API with GPT-5).

## The results

I ran the full matrix: both datasets, three token budgets, five random seeds, 100 examples per seed, GPT-5 as the summarization backend. Here are the action accuracy numbers, which measure whether the correct answer survived compaction:

| Condition | Baseline | Delta | Gap |
|-----------|----------|-------|-----|
| Oracle t140 | 9.4% | 9.0% | -0.4pp |
| Oracle t300 | 18.0% | 18.2% | +0.2pp |
| Oracle t1000 | 18.2% | 19.0% | +0.8pp |
| S t140 | 4.2% | 3.2% | -1.0pp |
| S t300 | 11.6% | 7.4% | **-4.2pp** |
| S t1000 | 8.2% | 10.0% | +1.8pp |

No meaningful difference. In most conditions the gap is noise, well within the standard deviation across seeds. On the S dataset at a 300-token budget, the delta method actually hurt, scoring 4.2 percentage points *below* baseline.

For what it's worth, an earlier version of my evaluation had a bug that made the delta method look spectacularly good (97.8% vs 13.2%). The eval was counting full conversation turns as valid answer matches instead of just the answer string itself. My extractor was accidentally preserving sentences that happened to contain answers, and the eval was accepting those as hits. Fixing that bug is what produced the table above. I mention this because it's the kind of mistake that's easy to make and hard to catch when the results are telling you what you want to hear.

## Why it didn't work

The extractor and the task were mismatched. My critical details module was built to catch safety constraints: negation patterns, modal verbs, approval codes. LongMemEval tests factual QA retrieval. "What was the person's name?" doesn't contain "do not" or "must not." The extractor was looking for a kind of information that wasn't in the data. On the synthetic benchmark, where I'd designed the test cases around exactly the patterns my regex looked for, of course it worked. That was a tautology, not a finding.

Dedicating 45% of the budget to the wrong signal is worse than no signal. When the critical details section fills up with irrelevant high-scoring sentences, that's token budget the summarizer can't use. The summary gets squeezed, and it loses information that actually matters for the task. The method doesn't just fail neutrally here. It actively degrades performance by misallocating the budget.

And regex just can't generalize. Even if the task had been right, a regex scorer can only catch patterns it was written for. If a constraint is phrased as "please ensure records are preserved" instead of "do not delete records," the extractor misses it entirely. For this approach to work on real data, the extraction step probably needs to be model-based, not pattern-based.

## What I still think is true

The core problem is real. Compaction drops important information, and sometimes that information is safety-critical. The Meta incident happened. The Claude Code GitHub repo has issues from users reporting that constraints from their CLAUDE.md files get lost after auto-compaction. This isn't a theoretical concern.

My specific solution didn't work on the benchmark I tested it against, but I don't think the general direction is wrong. A two-channel approach might still work with a better extractor — one that uses an LLM call to identify what's actually important rather than matching regex patterns. Or maybe the right approach is something different entirely: a verification step after compaction that checks whether any constraints from the original context got dropped.

I also think the synthetic results are still meaningful, even if limited. On conversations specifically designed to contain safety constraints, the dedicated extraction channel does preserve them better than a plain summary. The question is whether you can build an extractor that generalizes beyond the patterns it was designed for. I didn't get there.

## Context as a systems resource

One thing I keep coming back to from this project is how much LLM context management is starting to look like traditional systems engineering.

Context windows are a finite resource. You have a budget, there's pressure on it, and you have to decide what to keep and what to evict. That's memory management. Compaction is garbage collection — you're reclaiming space by throwing out what you think you don't need, and sometimes you throw out the wrong thing. Token budgets are bandwidth constraints. Retrieval-augmented generation is paging from disk.

People are building eviction policies, priority queues for what stays in context, hierarchical memory systems with hot and cold tiers. These are the same problems operating systems solved decades ago, showing up again in a completely different domain. I find that genuinely interesting, that the engineering patterns transfer even when the substrate is completely different.

This experiment was my attempt at building something like protected memory pages for LLM context. Critical instructions get pinned so they can't be evicted during compaction, the same way an OS pins pages that shouldn't be swapped out. My implementation didn't work. But I think the analogy holds, and as agents start taking real actions in the world, the engineering around context management is going to matter more, not less.

I didn't solve it this time. I'll keep pushing on it.

---

*All code, data, and results are at [github link]. The experiment uses LongMemEval ([paper](https://arxiv.org/abs/2410.10813)) and DialogSum ([paper](https://aclanthology.org/2021.findings-emnlp.479/)). Summarization was done with GPT-5 via the OpenAI API.*
