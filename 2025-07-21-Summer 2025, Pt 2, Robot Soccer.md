# Training robot soccer policies for RoboCup

Alongside my internship at Arm, I spent this summer training robot soccer policies for RoboCup. The individual skills worked well enough to compete, but the multi-agent coordination I wanted did not emerge reliably. We finished third with a hybrid system: learned skills underneath a heuristic team strategy.

I had wanted to join Peter Stone's lab since seeing it on a campus tour before I started at UT. I asked during freshman year, then asked again after gaining experience in other labs. When I joined in January, a graduate student pointed out the robots and told me, “We only have 6 robots right now, one of them is injured.” It took a moment to adjust to hearing a broken robot described that way.

## Learning the stack

I was new to reinforcement learning, so I built a small version of the team's 2D simulator and trained a simple policy in it. That gave me a tractable way to understand observations, actions, rewards, and the training loop before working inside the existing C++ project. The real codebase had more than 400,000 lines, so knowing what I was looking for mattered.

My first policies handled walking, dribbling, and shooting. I also changed simulator behavior where it failed to match the physical robots, including angular velocity during turns. A policy can exploit a difference between simulation and reality, so improving the simulator was part of making the learned behavior useful on hardware.

I wanted as much of the system as possible to be learned. The Bitter Lesson influenced that preference: build a method that can improve with training instead of adding a special case for every situation. I proposed a hierarchical action space, which we adopted. It let a higher-level policy select among lower-level skills rather than control every movement directly.

That approach worked for an individual attacker. A mid-level policy could switch between positioning, dribbling, and shooting. The successful skills gave me confidence that we could move up another level and learn cooperative play. That next step turned out to be much harder.

## Why cooperation stalled

Our curriculum began with a defender. We planned to train it, freeze it, then train attackers against it, eventually scaling from two attackers against one defender to full 7v7 play. The defender became strong before the attackers had learned enough to exploit the scenario. With standard PPO, the attackers repeatedly failed, including in the 2v1 setting.

We weakened the opposition. First we replaced the learned defender with a hard-coded ball chaser, then tried random actions. Those changes still did not produce reliable learning. I was treating difficulty mostly as a property of the defender, when it also depended on which successful behaviors the attackers could discover and receive credit for.

Before a demo, I started explaining the failed training to Peter Stone. He anticipated the result before I had finished. If the frozen defender wins almost every encounter and the attackers rarely receive a useful success signal, the training loop has little evidence about how to improve. A losing episode can still produce a gradient; the problem is that the gradient may not point toward the coordinated behavior we want.

That distinction stayed with me. Running the same difficult setup longer does not ensure that the agents will discover a strategy that provides a learning signal. The curriculum has to create situations in which progress is observable to the learner. I had spent weeks encountering a failure that he recognized from the structure of the problem.

We switched to MAPPO to give the training process a centralized critic. It appeared to help, but progress stalled again as we tried to scale. I did not establish that the critic alone solved the coordination problem. The result kept pointing back to the interaction between the task, rewards, and curriculum.

## Making iteration faster

As the competition approached, training throughput became another constraint. I noticed that the GPU was spending time idle and investigated the pipeline. Improving GPU acceleration produced a reported 200% speedup in training. That gave us more iterations, but it did not remove the learning problem in the multi-agent setup.

I also tried to turn soccer tactics into signals the policies could use. I watched material about Pep Guardiola's approach to positioning and space, then implemented grid-based rewards, pitch-control metrics, and expected-goals measures. We even replaced defenders with static obstacles to make passing opportunities easier to discover. Some runs looked promising, but none gave us reliable multi-robot cooperation.

The team eventually chose a heuristic top-level coach that assigned roles and switched policies. That let us use the learned individual skills within a team strategy we could control. I kept experimenting with multi-agent learning until the last day, while the team traveled to Brazil and I ran jobs remotely from home.

I rented more than 500 CPUs on AWS and spent late nights inspecting `wandb` loss curves and reward signals. I began stopping weak runs sooner instead of waiting for every job to finish. That improved how I allocated the remaining time, though it also made it important to avoid mistaking an early fluctuation for a meaningful trend. I normally take sleep seriously, so working past 1 AM repeatedly was a substantial change to my routine.

## What we brought to the competition

The hybrid system finished in 3rd place in our first competition in the more challenging league. My contribution was in the learned attacking skills and their hierarchical structure. The team-level coordination came from the heuristic strategy. I was proud of the result while still disappointed that we had not achieved the fully learned multi-agent behavior I had been aiming for.

The project changed how I evaluate an RL idea. A scalable method needs more than a large training budget. It needs a task distribution in which useful behavior can be discovered, a reward that distinguishes progress, and a simulator whose errors do not dominate the policy. Improving throughput matters because it lets us test those choices more quickly.

At the time of writing, we have a year until the next RoboCup. I want to continue from the coordination problem and understand which curriculum or learning setup would make the heuristic coach unnecessary. That remains a research goal, rather than a result I can claim from this summer.

One evening captured the personal side of the work. My routine had been to leave home at 7 AM after Fajr, go to the gym, work at Arm, then head to the lab. As I arrived, a CS tour group was passing the robots, with the same guide making the same jokes I remembered from my own visit. I had once stood there trying to work out how to join the lab. Now I was coming in to run the next experiment.
