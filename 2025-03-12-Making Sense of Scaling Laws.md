## The Three-Legged Stool of AI Progress: Why "Scaling Laws" Are Only Part of the Story

For the longest time, the narrative around AI progress felt a bit like a magic trick. We were told the secret was "scaling laws"—just add more data, more parameters, and more compute, and voilà, better models will appear. And for a while, that seemed to be true. But as I've been digging deeper, that explanation started to feel incomplete. It was like saying the secret to a great cake is just using more flour and sugar, ignoring the importance of the recipe, the quality of the ingredients, and the heat of the oven.

Then came DeepSeek. Here was a model that achieved state-of-the-art results, but seemingly with far less compute than the traditional scaling laws would have predicted. For some, this was a sign that the scaling laws were broken. For me, it was the final clue I needed to build a more complete mental model. DeepSeek didn't break the laws of scaling; it just highlighted that we've been focusing on the wrong parts of the equation.

This led me to a new way of thinking about AI progress, which I like to call the **Three-Legged Stool**. For a model to be stable and reach its maximum height (i.e., performance), it needs three legs to be in balance. If one leg is too short, the whole thing wobbles.

Here are the three legs:

### Leg 1: The Model (The "Recipe")

This isn't just about the number of parameters. It's about the *quality* and *efficiency* of the architecture itself.
*   **Architecture:** Are we using a Transformer, a CNN, a new hybrid model? The design of the model is the fundamental recipe.
*   **Parameters:** Yes, the size of the model matters. This is the raw "horsepower."
*   **Optimization:** This is the secret sauce. Techniques like AdamW, dropout, and clever new methods like LoRA are the difference between a powerful model and a powerful model that can actually be trained effectively.

### Leg 2: The Data (The "Ingredients")

We've all heard "garbage in, garbage out." This leg is about the fuel for our models.
*   **Quantity:** The sheer volume of data is obviously important.
*   **Quality:** This is the part that I think is becoming increasingly critical. Is the data diverse? Is it clean? Is it representative of the problems we want to solve? A smaller dataset of incredibly high-quality, diverse information can be far more powerful than a massive dataset of internet noise.

### Leg 3: The Compute (The "Oven")

This is the raw energy that makes everything happen.
*   **Raw Power:** How many GPUs or TPUs can you throw at the problem?
*   **Compute Efficiency:** This is the sub-constraint that DeepSeek brought into the spotlight. Innovations like FlashAttention or optimized kernels are like a convection oven—they allow you to cook faster and more evenly with the same amount of energy. This is a huge force multiplier.

### Putting It All Together: A Back-of-the-Napkin Formula

The DeepSeek example fits perfectly into this framework. It didn't have the most "Raw Power" in its compute leg, but it had a massive advantage in "Compute Efficiency" and likely a very strong "Model" leg (a better recipe). This allowed it to stand just as tall as models that were relying on a much longer, brute-force compute leg.

To crystallize my own thinking, I sketched out a conceptual formula. This isn't a proven law; it's just a tool for my own intuition:

\[ \text{Performance} \approx k \cdot (\text{Model}^{\alpha} \cdot \text{Data}^{\beta} \cdot \text{Compute}^{\gamma}) \]

Here, \(M\), \(D\), and \(C\) represent the "quality" of each of the three legs, not just their raw size. The exponents (\(\alpha, \beta, \gamma\)) represent how much each leg contributes to the final performance, and I suspect their relative importance changes as the whole field evolves.

### Why This Matters to Me

This framework isn't just an academic exercise. As a builder, it gives me a structured way to think about where the next big breakthroughs will come from. It tells me that we shouldn't just be looking at the teams with the most GPUs. We should be looking at the teams with the most clever architectural tweak, the highest-quality dataset, or the most efficient training algorithm.

It's a reminder that progress in AI isn't a monolithic march of scale. It's a complex, interconnected dance between these three fundamental constraints. And the teams that understand how to balance all three legs of the stool are the ones who will build the future.

