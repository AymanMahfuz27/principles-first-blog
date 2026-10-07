# Compression, explanation, and understanding

A model can produce a correct answer without giving us a useful account of why it is correct. That matters if we want AI to contribute to science. A prediction may help us choose an action; an explanation can tell us which relationship to test, where the prediction will fail, and how to apply the result elsewhere.

I think explanation deserves more attention as a capability in its own right. It should be evaluated alongside task performance, because a fluent justification can be wrong even when the answer is right. The challenge is measuring whether the explanation captures something real about the problem or the computation that produced the answer.

## The soccer example

When I watch soccer, I can sometimes sense that a goal is becoming more likely before I can explain the change. The attacking team may have pinned the defense back, improved its passing rhythm, or started creating space in a particular area. Repeated exposure makes those patterns recognizable.

A tactical explanation goes further. It identifies an overlap that creates a two-against-one, a midfielder who pulls a defender away, or a delay that opens a passing lane. Those claims can be checked against the play. They also help explain why a superficially similar attack might fail.

The distinction I care about is between recognizing a pattern and identifying relationships that let someone else reason about it. Both are useful. An explanation becomes more valuable when it makes predictions that the original intuition did not make explicit.

## What learned models make difficult

Earlier chess systems often used evaluation functions with hand-designed terms. That made parts of the scoring process easy to inspect, though inspecting those terms did not explain every search decision. Learned systems such as AlphaZero replaced much of that evaluation with a neural network trained through self-play. They still use game rules and search; the learned evaluation is the part whose reasoning is harder to express in ordinary language.

Language models complicate the distinction because they can generate an explanation on request. The explanation is another output of the model. Its coherence does not guarantee that it faithfully describes the internal computation responsible for the answer. It may instead be a plausible account assembled after the answer was chosen.

There are also two different targets for explanation. We might want to understand how the model computed an answer, or understand the external phenomenon it predicts. A faithful account of the first does not automatically provide a scientific theory of the second. A model could rely on a useful correlation that has little to do with the underlying cause.

## What I would want a benchmark to measure

A strong explanation benchmark would test consequences, rather than reward persuasive prose. In chess, a model might explain why a move works and predict how the evaluation changes when a piece moves. In biology, it might propose a mechanism that leads to an experiment with a distinguishable outcome. In cosmology, it might turn a pattern in observations into a hypothesis with predictions beyond the original dataset.

These tests would still need domain-specific standards. Mathematical claims require proof, causal claims require appropriate evidence, and uncertain predictions require calibrated confidence. A single score for sounding reasonable would reproduce the problem we were trying to solve.

My interest in explainability comes from this transfer. If a model has learned structure that supports strong predictions, can it express enough of that structure for people to test and use? The value would be more than reassurance about an answer. It would be knowledge that survives scrutiny and helps us design the next investigation.
