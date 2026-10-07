# Making sense of scaling laws

Scaling laws helped me understand why increasing model size, data, and compute can produce predictable improvements. They also left me with a question: if scale is so important, how do changes in architecture or training efficiency shift what we get from the same budget? DeepSeek made that question harder to ignore. Its results suggested that comparing GPU counts alone was an incomplete way to compare systems.

I do not think those results break scaling laws. An empirical relationship is measured under particular choices of architecture, data, and training procedure. Changing those choices can change the relationship. A more efficient system may reach a given loss with less compute while still following a regular scaling trend within its own setup.

## Three resources that interact

The **model** determines what computation we perform and how effectively it can use the data. Parameter count matters, but so do the architecture and training procedure. An optimizer such as AdamW changes how the weights are updated; regularization such as dropout changes the learning problem. LoRA addresses a different issue by reducing the number of parameters updated during fine-tuning. Treating all three as interchangeable efficiency techniques would hide what each one actually saves.

The **data** determines the patterns the model has the opportunity to learn. More tokens help only to the extent that they add useful training signal. Diversity, quality, repetition, and relevance to the intended tasks affect that signal. A smaller dataset can outperform a larger one in some settings, but quality does not eliminate the need for sufficient coverage.

Compute determines how much of the learning process we can run. Hardware throughput, memory capacity, communication, and kernel efficiency all affect the useful work we get from a budget. Techniques such as FlashAttention reduce the cost of a particular operation. That lets us use the savings for a larger model, more data, or more experiments, depending on which constraint matters most.

These resources interact. A larger model that sees too little data may use compute poorly. A good architecture can still be limited by a weak dataset. Faster hardware does little for a pipeline that spends its time waiting on data or communication.

## What my informal formula was trying to capture

I originally sketched the following relationship as a way to organize my intuition:

\[ \text{Performance} \approx k \cdot (\text{Model}^{\alpha} \cdot \text{Data}^{\beta} \cdot \text{Compute}^{\gamma}) \]

Here, the three terms stand for the quality and quantity of those resources, and the exponents stand for their relative influence. This is not a fitted scaling law. The terms are not defined precisely enough to calculate with, and a single product cannot capture every interaction. Its only purpose is to remind me that a resource has value through the system that uses it.

The published work is more specific. [Scaling Laws for Neural Language Models](https://arxiv.org/abs/2001.08361) measures relationships between loss and resources under controlled settings. [The Chinchilla study](https://arxiv.org/abs/2203.15556) examines how to allocate a training compute budget between model size and data. Those are empirical questions, rather than statements that bigger is always better.

For my own projects, the consequence is practical: identify the limiting resource before increasing it. If the next improvement comes from better data, buying more compute may only make the same mistake faster. If the training pipeline is inefficient, fixing it can increase the number of ideas I can test without changing the hardware.
