## My Summer of Robot Soccer: A Crash Course in RL, Late Nights, and a Bronze Medal

Alongside my internship at Arm this summer, I took on another challenge that would consume my nights and weekends: **teaching a team of robots to play soccer for the international RoboCup competition**. What followed was a whirlwind of reinforcement learning, sleepless nights, and some of the most difficult and rewarding engineering problems I’ve ever faced.

Here’s the story of how it went down.

### Part 1: Onboarding and a Winning Streak

Getting into this lab was a dream of mine before I was even a student at UT. I saw it during a campus tour and was instantly hooked. I asked the head professor, Peter Stone, if I could join in my freshman year, but it didn't work out. After getting more experience in other labs, I asked again—and this time, I was in. Walking into the lab for the first time as a team member and seeing the robots in person was an amazing moment. A grad student pointed to the lineup and said, "We only have 6 robots right now, one of them is injured" (???what).

I joined the team in January as a total newcomer to **reinforcement learning (RL)**. To get up to speed, I decided my first move should be to build a toy version of our 2D simulator from the ground up and train a simple policy. It was the perfect way to learn the fundamentals before diving into the team's existing codebase—a sprawling, **400,000+ line C++ project** that was, to put it mildly, intimidating.

Once on board, I got my hands dirty immediately. The first mission was to train the **low-level policies** for the robot's basic skills: walking, dribbling, and shooting. But I wasn't just coding policies; I was thinking about the endgame. My core philosophy, heavily inspired by AI's **“Bitter Lesson,”** was that we should build a single, scalable policy that could master 7v7 soccer with enough training, minimizing manual tweaks. I learned that was *far* easier said than done, but it drove my approach. I pitched the idea of a **hierarchical action space**, a concept we ultimately adopted, and it was incredibly cool to see it come to life.

While working on these policies, I found myself frequently diving into the simulation code to better mimic reality. I had a blast hacking away in the C++ guts of the simulator to optimize things like a robot's angular velocity so its turning motion in the sim matched its real-world counterpart.

It was during this time that I got a feel for the lab's culture: multiple research threads attacking the same problem in parallel. Many of my teammates favored hard-coded, classical robotics solutions. But a sense of pride pushed me to go all-in on the AI approach. My bet was that if we could make our system as **AI-native** as possible, it would be more powerful and ultimately, more successful.

And for a while, that bet paid off. My RL-based policies for **walking, dribbling, and shooting** consistently worked, and worked well.

Buoyed by this success, I moved up the stack to a **mid-level hierarchical policy**. The goal: a smart attacker that could decide for itself which skill to use. Again, I stuck to my guns and built it with RL, and again, it worked. My agent was able to intelligently switch between dribbling, positioning, and shooting.

### Part 2: The Multi-Agent Wall

With the individual skills looking solid, we turned to the great challenge: **multi-agent coordination**. The dream was to have cooperative plays like passing emerge entirely from learning. Our plan was to use a **curriculum approach**: train a strong defender, freeze it, and then train two attackers against it to force cooperation. We’d repeat this cycle, scaling up to a full 7v7 team.

I trained a defender, and it learned its job a little *too* well. It became a monster. Our attackers, using standard **PPO**, couldn’t do anything against it. We scaled to a 2v1 scenario, but the two attackers were just as helpless. My super-defender was an impenetrable wall, and since the attackers never succeeded, they weren't learning anything.

Frustrated, we started making the defender progressively dumber. We replaced the learned agent with a hard-coded one that just chased the ball. Still too hard. We made it take random actions. Still too hard.

It was during this struggle that one of the most memorable moments of the project happened. I was about to demo my failing 2v1 training to my professor, Peter Stone, and explain how the attackers couldn't learn against the frozen defender. Before I even finished my sentence, he predicted the outcome. He explained that if the defender is a fixed policy that always wins, there's **no learning gradient** for the attackers to follow. They get no signal on what they could have done differently. I had spent weeks of experimentation to arrive at a conclusion he reached from pure theory. It sounds so obvious now as I type it, but at the time it never clicked for me, and that lesson stuck with me.

We were advised that **MAPPO (Multi-Agent PPO)** might work better by providing a centralized critic, so we made the switch. This seemed to help, but as we tried to scale further, progress stalled again.

### Part 3: The Grind

With pure RL not yielding the results we needed, I started looking for inspiration elsewhere. I spent hours watching documentaries on soccer coaches, particularly **Pep Guardiola**. His tactical concepts, like dividing the field into zones, felt programmatic and rewardable.

At the same time, training times became a **massive bottleneck**. We had limited compute, and with the competition drawing closer, every hour was precious. I noticed our GPU sitting idle and decided to take matters into my own hands. I dove into the pipeline and aggressively optimized our code for **GPU acceleration**. The result was staggering: **a 200% speedup in training**. It was an insane win.

Even with faster experiments, emergent coordination remained elusive. I tried everything: I coded up metrics for **"Pitch Control"** and **"Expected Goals (xG)"** to guide the rewards. I created grid-based rewards inspired by Guardiola's tactics. We turned the defenders into literal **"cones"**—static obstacles to encourage passing. We got tantalizingly close, but never cracked reliable multi-robot cooperation.

As the competition loomed, the team made the tough but necessary call to fall back on a **heuristic-based system** for multi-agent play. A top-level "coach" would assign roles and switch policies for the robots. While the team focused on perfecting that, I stubbornly kept pushing on the multi-agent problem until the very last day.

The final weeks were a blur. While the team traveled to Brazil for the competition, I stayed up late at home, running experiments remotely. I even rented **over 500 CPUs on AWS**, but it still wasn't enough. My entire workflow changed. I stopped letting runs go to completion and instead started meticulously monitoring **`wandb`** loss curves and reward signals, hunting for any hint of promise. I take my sleep very seriously, so being up past 1 AM every night was a new and draining experience, driven by the hope that the next experiment might just be the one.

### Part 4: Victory and a Look Ahead

In the end, our hybrid approach was a resounding success. The combination of my RL-trained individual skills and the team's robust heuristic strategy earned us a **3rd place finish**. It was an incredible achievement, especially given it was our first time competing in this more challenging league. On a personal note, while it sucked that the robots weren't multiagent, I was proud that at least the individual attacking policies, a heriearchical structure of lower level strategies, was something I had my fingerprints on. 

This project was, without a doubt, one of the hardest things I've ever taken on. The experience was invaluable. I learned so much from my talented teammates about how to experiment and iterate quickly. Looking back, I’m incredibly proud of what we accomplished, but I'm also hungry for more. That bronze medal feels like a down payment on gold.

We have a year until the next RoboCup, and I'm starting work on it today. My goal is to pick up where I left off and **crack the multi-agent problem**. The mission is clear: to replace the heuristics with learned, emergent intelligence, and come back next year to turn that bronze into gold.

A small interaction that happened that I though was nice: My schedule this summer was a testament to the grind: I’d leave the house at 7 AM after Fajr prayer, hit the gym, go to my internship at Arm, and then head straight to the UT lab to work on the robots. One evening, as I was coming into the lab, I saw a tour group for the CS department walking by—the same tour I had taken years ago. The tour guide, my old tour guide, was making the exact same jokes I remembered. The prospective students were peering into the lab with the same wide-eyed look I once had. This time, I was the student researcher and not the high schooler looking to get in. Seeing how far I'd come was a heartwarming, full-circle moment. It was a good reminder that sometimes, it’s nice to appreciate the progress you've made in the midst of ambition, curiosity, and uncertainty.