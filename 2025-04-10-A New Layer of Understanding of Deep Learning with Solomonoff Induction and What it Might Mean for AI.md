# **A New Layer of Understanding: Compression, Solomonoff Induction, and the Future of Deep Learning**

Over the past few months, I’ve been trying to piece together a better intuition for how large language models actually work.  
In my earlier posts — *"A Speculative Exploration of the Untapped Intelligence of LLMs"* and *"An Intuition About Transformers"* — I leaned heavily on the idea that prediction drives compression, and that compression forces abstraction.  
It felt like I was starting to glimpse something real: that maybe intelligence wasn't being programmed directly into these systems, but was emerging as a side effect of prediction and structure-building.

Recently, that intuition deepened in a way I didn’t expect.

While listening to a conversation about how GPT-4.5 was trained, I came across an explanation by OpenAI researcher Dan Selsam that left a lasting impression on me.  
When asked why unsupervised learning works so well, he answered with one word: **Compression.**  
Then he explained that the ideal form of intelligence is something called **Solomonoff Induction** — the idea that the best way to predict the future is to consider all possible explanations for the past, but weight them by their simplicity.  
Shorter, simpler explanations are far more likely.

Hearing that made immediate sense.  
It felt like a Bayesian formalization of **Occam’s Razor** — something simple, clean, and powerful underneath it all.  
And in a strange way, learning about Solomonoff Induction felt more like something I would have expected from an algorithms class rather than a statistics course — a shift from viewing deep learning through the lens of **data and patterns** to **compression and computation**.

It also triggered a larger realization.

In my earlier reflections, I had written about how machine learning once felt like programming — lots of trial and error, engineering, intuition.  
And how it later began to feel more like **physics** — scaling laws, emergent behaviors, predictable trends.  
There was something profoundly beautiful about it — as if beneath all the noise and messiness of training neural networks, there were **underlying currents** shaping everything.  
Scaling laws made deep learning feel like the discovery of physical laws — simple, elegant patterns emerging from seemingly chaotic systems.

But now, this realization about compression and Solomonoff Induction made me feel that the current runs even deeper.  
Beneath the physics, beneath the scaling laws, there’s something more fundamental still: **mathematics**.  
Not mathematics in the sense of statistics and averages, but mathematics in the form of *complexity*, *compression*, and *computability*.  
Something about learning **the shortest program** that explains the world — and updating that program as new evidence arrives.

Deep learning, viewed through this lens, isn’t just about pattern-matching or fitting data anymore.  
It’s about **compression of reality** into the simplest, most efficient internal models possible — with reasoning, abstraction, and even creativity emerging as natural byproducts of that compression.

This realization doesn’t replace anything I believed before — it adds another layer underneath.

- Where I used to think about latent capabilities hidden inside models,  
- now I realize those capabilities emerge from the compressed representations the model builds through training.
- Where I used to think about fine-tuning and prompting as ways to "surface" better behaviors,  
- now it feels like what we’re really doing is nudging which compressed explanations the model favors internally.

And where I once thought of deep learning evolving from engineering to physics,  
now it feels like the story is even richer:  
**a journey from programming → to physics → to the deep mathematics of compression and computation.**

---

As I thought more about these ideas, a new question emerged:  
**Where does structure stand in the way of scaling laws?**

There’s a long history in AI of trying to engineer structure manually — and seeing scaling sweep it away.

For example, early versions of GPT-2 struggled with simple arithmetic, leading researchers to experiment with explicit arithmetic modules.  
It made sense at the time: addition and subtraction felt like things you should manually scaffold into a model.  
But when GPT-3 came along — trained at a much larger scale — it picked up basic arithmetic naturally without any explicit hardcoding.  
Simply by scaling up, the model discovered patterns we thought would have required special modules.

It’s not an isolated case.

When researchers first built programs to play chess, they hand-crafted value functions.  
They manually defined what mattered in a board state — material advantage, king safety, control of the center, and so on.  
These programs performed decently, but they were brittle and easily surpassed.

When deep reinforcement learning arrived, paired with large neural networks, something changed.  
Instead of hard-coding rewards, models were trained to discover their own internal structures.  
AlphaGo, AlphaZero, OpenAI Five — all found strategies and abstractions that human engineers hadn’t even considered.

It happened again in computer vision.  
For years, hand-designed features like SIFT and HOG dominated the field.  
Researchers meticulously engineered pipelines to extract edges, corners, textures.  
Then AlexNet came along — a relatively simple convolutional network, trained on enough data — and learned features better than anything humans could design manually.  
And after that, deep learning didn't just outperform old methods — it completely transformed the field.

Everywhere we thought *"you must engineer structure,"*  
**deep learning and scaling proved we were wrong.**

Neural networks are the ultimate feature extractors.  
Given enough scale, enough diversity of environments, and enough pressure to compress the world efficiently, they discover structures we couldn’t have manually specified even if we tried.

And so my take is this:  
Scaling, when done wisely, should remain the primary strategy.

If rich enough data, strong enough multimodal experiences, and enough computational pressure are applied, **models may naturally discover reasoning**, abstraction, planning, and world modeling — just as they discovered features in vision and strategies in games.

Maybe the best way to build reasoning systems isn't to engineer logic modules or theorem provers into neural nets.  
Maybe it’s to **scale models in the right environments** and **let compression and emergence do the heavy lifting** — again.

It’s a big bet.  
And it’s the bet OpenAI seems to be making right now.

Scale first.  
Let compression do the heavy lifting.  
Only introduce explicit structure **if** and **when** scaling stalls — not before.

And honestly, history is on their side.

---

At the same time, I have to wonder:  
Maybe there are limits to this analogy.  
Maybe the kind of reasoning we want — deeply reliable, self-verifiable, combinatorially generalizable reasoning — is **different** from vision and games.  
Maybe it isn’t as smooth and compressible.  
Maybe it demands symbolic scaffolding to avoid brittleness at the frontier.

I don’t know yet.  
And that's part of what makes this so exciting to think about.

---

What I do know is that hearing Dan Selsam explain Solomonoff Induction shifted something in me.

It made me realize that deep learning isn’t just about scaling patterns.  
It’s about **scaling compression**.  
It’s about finding the shortest, simplest explanations for reality that still predict the future well.

And what really resonates with me is how this fits into the way I’ve been learning all along.  
In my earliest blog posts, I talked about understanding things from *first principles* — but backwards.  
I started with programming, then statistics, then physics, and now something deeper: compression, complexity, computation.  
At the time, it didn’t feel like a clean, forward logical progression — but looking back, it’s exactly what’s happening.  
The understanding is being built layer by layer, backwards through experience, until the deeper principles reveal themselves.

Now, when I think about the future of AI, I don’t just see bigger models or better engineering.  
I see a deeper, more beautiful story unfolding — a story about compression, simplicity, and intelligence emerging naturally from the mathematics that have been here all along.

Maybe the future really is hidden inside the shortest programs waiting to be found.

And maybe deep learning was never just about data after all.  
Maybe it was always about mathematics.

