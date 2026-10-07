# Prediction, compression, and transformer representations

The part of transformers I found hardest to understand was the gap between the training objective and the resulting behavior. The model is trained to predict the next token, yet it can write code, follow an argument, and answer questions about a story. I understood the architecture before I had a useful explanation for why that objective could produce such broad capabilities.

Two examples helped me form an intuition. In image classification, networks can learn progressively more useful features: edges and textures can contribute to shapes, and shapes to objects. In a discussion between Jensen Huang and Ilya Sutskever, Ilya used a murder mystery as an example of prediction requiring more than local word statistics. To predict the killer's name at the end, a model might need to track evidence introduced much earlier.

That suggested a connection: a prediction task can reward representations of the structure that generates the observations. If tracking characters and motives improves the next-token prediction, the training objective gives the model a reason to learn those relationships. We do not have to specify a separate loss for every relationship we want it to discover.

## The hierarchy I had in mind

I imagined a **prediction cascade**. Early computation captures relatively local patterns, later computation combines them, and the resulting representation supports a better prediction. At the simplest level, the model learns regularities in tokens and syntax. It can also learn factual associations, such as which name tends to complete a sentence about the first person to walk on the moon. More demanding predictions may depend on relationships across a whole passage.

This is a mental model, rather than a literal description of what each transformer layer does. All layers contribute to the same next-token objective; the early layers are not separately trained to predict characters, and the late layers are not given a distinct semantic prediction task. Information can be distributed, reused, or transformed in ways that do not fit a tidy progression from grammar to reasoning.

The murder mystery illustrates the incentive without proving the mechanism. A correct prediction could result from tracking clues, but it could also come from memorizing the story or exploiting a common narrative pattern. To claim that the model inferred the culprit, I would need examples that rule out those alternatives. The behavior alone does not tell me which representation produced it.

## Why compression helps the explanation

Memorizing every possible sequence is impractical. A model that reuses patterns can represent more of the data with fewer independent rules. Grammar is one such pattern; relationships between entities and events are others. Learning those structures can improve predictions on sequences the model has not encountered before.

That is what I mean when I connect prediction, compression, and abstraction. Prediction rewards useful regularities. A compact representation can reuse them across examples. Some of those representations support behavior we describe as understanding, though that last step needs more evidence than a low prediction loss.

This explanation changed how I think about model design. We can create a learning objective that rewards useful structure and let training discover much of that structure. The remaining questions are what the model actually learns, how reliably it uses it, and where the prediction objective leaves important gaps. Those are questions I can investigate with experiments, rather than settle through the intuition alone.
