# Principles First

I started this blog because I want to understand how the systems I use actually work. Using a model and understanding it are different levels of knowledge. I can get useful output from a transformer without knowing why attention helps, how the training objective shapes its representations, or which parts of its behavior remain unexplained. I want to close that gap.

My approach is to start with a system that interests me and work backward through its dependencies. A new architecture leads to a training method, the training method leads to an optimization problem, and the optimization problem leads to the mathematics underneath it. The point is to build a mental model I can use when the implementation changes, rather than memorize the current vocabulary.

Writing makes that process harder to fake. An idea can feel clear while I'm reading a paper because the author supplies the missing steps. Explaining it myself exposes the places where I've accepted a claim without understanding the mechanism. Those gaps usually tell me what to study or test next.

I'm Ayman. I work on software and machine learning, and I want to build systems whose behavior I can reason about. This blog will include explanations of papers, intuitions I'm developing, and experiments that test those intuitions. Some of the ideas will be incomplete. I'll distinguish what I observed from what I think it means.

The useful endpoint is an explanation that survives a new example. If I understand why a technique works, I should be able to say when it will fail, what assumptions it depends on, and how I might adapt it. That's the standard I'm trying to hold myself to here.
