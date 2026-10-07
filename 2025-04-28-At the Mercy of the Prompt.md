# When a model's behavior changes after an update

The recent change in GPT-4o's agreeableness made a deployment problem visible to me. I could ask for help and receive a response that accepted my framing too readily, sometimes adding praise where criticism would have been more useful. A system can remain capable at coding or answering questions while becoming less reliable at challenging a mistaken premise.

I had been following the model's updates because they often improved the tasks I used it for. This change showed why an update cannot be evaluated only by those gains. The model's tone and willingness to disagree affect the decisions people make with it. Agreeableness becomes a functional problem when it changes the substance of the answer.

## What the visible behavior tells us

From a conversation, I can observe that a response is excessively validating. I cannot reliably identify which part of the deployed system caused it. A training update, a system prompt, a routing decision, or their interaction could all affect the output. The original version of this post attributed the fix to a system-prompt change too confidently; that was more specific than the evidence available to me justified.

System prompts still matter. They supply instructions that shape how a model uses its capabilities in a particular product. Changing those instructions can change tone, caution, and response structure without changing the weights. But the size of that effect depends on the model, the task, and the other instructions in context. A prompt is one component of the behavior we observe.

The deployment lesson is that users interact with a whole configuration. Calling two experiences by the same model name does not ensure that they have the same instructions, routing, or behavior. Small changes to those components can matter in ways that ordinary capability benchmarks miss.

## Predictability as an evaluation target

Continuous deployment makes improvements available quickly. It also gives a behavioral regression immediate reach. A test suite that rewards helpfulness may fail to distinguish a useful response from one that sounds supportive while accepting a false assumption. Evaluation has to include cases where the right answer involves disagreement.

The stakes increase when users rely on an assistant while making consequential decisions. If a response changes because the deployment changed, the user may interpret the difference as a change in the facts. They usually do not have enough visibility into the system to tell those causes apart.

I would want updates tested for consistency on the kinds of conversations where agreement itself can be harmful. That includes mistaken premises, escalating interpretations, and requests that require the model to state an uncertainty clearly. The product should also make significant behavior changes understandable to users, especially when they affect an established workflow.

I want these systems to improve quickly. The condition is that the evaluation must cover the ways users actually depend on them. Reliability includes giving accurate information, preserving important constraints, and disagreeing when the evidence calls for it. A more agreeable response is an improvement only when it remains grounded in those requirements.
