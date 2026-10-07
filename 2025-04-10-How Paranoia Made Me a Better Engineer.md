# How paranoia made me a better engineer

I learned to design for failure after losing a large data collection run and the API budget that paid for it. I was collecting metadata for millions of articles through Diffbot. I used the single-request endpoint instead of the batch endpoint and kept the accumulated results in one in-memory DataFrame. When the virtual machine ran out of memory, the process crashed before I had written the data to disk.

The API calls had already been billed. I had used the lab's monthly credits in an afternoon, and the work those credits purchased was gone. Restarting the script could not recover the data or undo the cost. The failure came from decisions I had made about resource use and persistence, rather than an unpredictable defect in the machine.

Before that, I treated many pipeline failures as a matter of lost time. A script stopped, I fixed it, and I started again. That mental model stopped working once the script consumed something I could not recover. A long run with paid inputs needs to preserve progress while it is running, not only after it succeeds.

I rebuilt the pipeline around that requirement. I added checkpoints and validated the inputs and outputs instead of assuming that a request producing a response meant the whole operation had worked. I started thinking through what would survive a crash and which assumptions could become expensive at scale. That version of the pipeline went on to populate the lab's database with over 200 million entries.

The change was in what I considered a complete implementation. Producing the right result once was no longer enough. I needed to understand whether a run could stop safely, resume without repeating paid work, and reveal when the output was wrong. A pipeline's failure behavior became part of its design.

## Why the habit stayed

Later, I worked on vision transformers for 3D MRI segmentation in a lab with an H100 and much more compute available. The immediate budget pressure was lower, but I still checked the data pipeline and configuration before launching long runs. I tracked loss curves daily because access to more resources did not make an invalid experiment more useful.

The cost of an error includes the work that follows from it. A wrong configuration can consume compute, but it can also produce results that send the next experiment in the wrong direction. A silent data problem is worse than an obvious crash if I interpret it as evidence about the model. More capacity increases the amount of work I can do before discovering that the premise was wrong.

I call this habit paranoia, though the useful part is specific. Identify the assumptions that could invalidate the work, make important state durable, and put checks where they can catch an error before it spreads. Checking everything repeatedly without a failure model would consume attention without necessarily improving reliability.

That first mistake changed how I think about trust in a system. Confidence should come from knowing which failures it can detect and recover from. I still expect components to fail. The engineering work is making those failures bounded, visible, and recoverable.
