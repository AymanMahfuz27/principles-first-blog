# Compression and Solomonoff induction

Solomonoff induction gave me a more precise way to think about the connection between prediction and compression. I had been developing the intuition that next-token prediction rewards a model for learning reusable structure. Hearing an OpenAI researcher discuss compression led me to ask what an idealized version of that process would look like.

The appeal is straightforward: if a compact rule explains many observations, it may also make useful predictions about observations we have not seen. A description that merely lists the past data does not offer the same kind of generalization. This is the connection to Occam's razor that initially caught my attention.

## What the formal idea says

**Solomonoff induction** assigns more prior weight to shorter programs and uses programs consistent with the observations to predict what comes next. It combines possible explanations rather than simply selecting the single shortest program. The reference machine matters to the description of program length, and the general procedure is incomputable. It provides a theoretical model of induction, not an algorithm we can run at full generality.

[Solomonoff's discussion of algorithmic probability](https://raysolomonoff.com/publications/isis96.pdf) makes the distinction between the theoretical method and practical approximations explicit. That distinction matters when connecting it to deep learning. Training a neural network is not equivalent to enumerating programs under a universal prior, and a neural network's parameters are not a direct measurement of the shortest program that explains its data.

The connection I find useful is conceptual. Both perspectives give a reason to care about structure that can explain many examples compactly. A language model may reuse grammatical, factual, or relational patterns to improve prediction. Solomonoff induction helps me articulate why such reuse could support generalization, without proving that a particular model learns the right structures.

## How this fits my earlier understanding

I first approached machine learning as engineering: write the pipeline, choose the architecture, and improve the training run. Scaling laws added an empirical layer by showing regularities across runs. Information theory and algorithmic probability ask a different question about what makes an explanation useful for prediction.

These perspectives complement each other. The engineering determines what system we can train. The empirical work tells us how that system behaves. The theoretical view suggests questions about why some representations generalize. I do not have a derivation showing that observed scaling laws follow from Solomonoff induction, and the connection should not be presented as one.

## Learned structure and designed structure

This also changes how I read the debate over scale and hand-engineered knowledge. Language models can learn arithmetic patterns from data. AlphaZero learned strong game policies through self-play. AlexNet learned visual features that earlier systems often specified manually. In each case, learning replaced some work that people had previously encoded by hand.

Those systems still contain substantial design choices. AlphaZero combines a neural network with search and known game rules. AlexNet uses convolutional structure. Transformers have their own architectural constraints and optimization procedures. Their success supports giving learning room to discover useful representations; it does not show that every prior or engineered component should be removed.

The question I want to carry forward is which structures are better learned and which make learning more effective when we supply them. Compression gives me a way to think about that question, but the answer has to come from comparisons. A satisfying theory becomes useful when it tells me what to measure and what result would change my mind.
