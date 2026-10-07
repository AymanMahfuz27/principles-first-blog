# What capabilities are already in a pretrained model?

I have been trying to separate two questions about language models: what training teaches them, and what makes that learning accessible at inference time. A pretrained model can contain useful information without reliably producing it in response to a request. Prompting and post-training can change that reliability. I want to understand how much improvement comes from eliciting an existing capability and how much comes from learning a new one.

This is a speculative post. I do not have evidence for a coherent intelligence hidden underneath the model's ordinary behavior. As of March 3, 2025, [Reasoning with Latent Thoughts: On the Power of Looped Transformers](https://arxiv.org/abs/2502.17416) gives me another reason to investigate internal computation, but it does not establish the broader hypothesis I'm considering here.

## What post-training changes

My initial intuition was that reinforcement learning from human feedback mainly teaches a model how to present what it learned during pretraining. It rewards useful answers, appropriate behavior, and responses that match a user's intent. That can make a capability much easier to access without teaching the underlying subject from scratch.

The distinction is less clean than that intuition suggests. Post-training changes the weights, so it can also change what the model knows and which strategies it learns. We cannot classify every gain as either presentation or new knowledge by inspecting the final answer. We need comparisons that hold the task and evaluation fixed while changing the training or elicitation method.

## Why prompting and extra compute matter

Chain-of-thought prompting made this question concrete for me. Asking a model to produce intermediate steps can improve its performance on reasoning tasks. One interpretation is that the prompt elicits a strategy the model already learned. Another part of the effect may come from giving it additional tokens on which to compute. Better output does not by itself tell us which internal mechanism changed.

Test-time compute introduces a related ambiguity. Sampling more candidates and selecting among them can find a correct answer even when most attempts fail. A longer reasoning trajectory may also improve a single attempt. Both can increase accuracy, but they have different costs and imply different things about the model's capabilities. I would want to distinguish them before saying that more compute uncovers deeper understanding.

This is why I am interested in access to internal representations. If useful computation is present but inconsistently elicited, perhaps an intervention could make it more reliable. Work on induction heads shows that some learned mechanisms can be identified and studied. It does not follow that there is a single reasoning switch we can activate, or that suppressing an apparent shortcut will improve behavior across tasks.

## What I would want to test

The useful version of this idea is measurable. Start with a task the model sometimes solves, identify a candidate representation or mechanism, and intervene on it. Then test whether the improvement holds on new examples, whether other capabilities degrade, and whether the effect survives controls. That would be stronger evidence than finding a prompt that happens to work well.

I still think there is room to improve how we use pretrained models. Some gains will require new training, some better inference procedures, and some a clearer understanding of the representations already learned. My interest is in distinguishing those cases so we can spend compute on the part that actually limits performance.

Papers that shaped this question:

- Ziegler et al. (2019), *Fine-Tuning Language Models from Human Preferences*.
- Wei et al. (2022), *Chain-of-Thought Prompting Elicits Reasoning in Large Language Models*.
- Olsson et al. (2022), *In-Context Learning and Induction Heads*.
- Kaplan et al. (2020), *Scaling Laws for Neural Language Models*.
