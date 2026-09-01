Learning control through trial and error - **Reinforce** good behaviour with **rewards**. Learning a good control strategy with rewards.

Start with an agent and environment. Agent has agency to interact with the environment. Agent measures the state of the environment. This state ($\mathcal{S}$) visibility is limited to what it knows. Then based on this limited state observation, the agent performs an action ($\mathcal{a}$). Very occassionally, throughout the problem, the agent gets a reward ($\mathcal{r}$). The agent attempts to learn the pattern behind this reward. This is called [[Semi-Supervised Learning]], where the reward is sporadic and delayed. If the agent gets a reward every time, it will be [[Regular Supervised Learning]], and the rewards are called labels. **Supervised** in this context refers to the fact that there is a supervisory feedback throughout the learning process telling the agent what worked and what didn't.

Major challenge in reinforcement learning is that it is difficult to tell **exactly what actions led to the reward**, as the feedback is delayed and sporadic. This presents a difficult optimisation problem which the agent has to figure out which action led to the reward through trial and error and data. Another significant challenge is to design a policy of **what actions to take given a state** to **maximise the chance** of getting a future reward. The only thing agent can do is to **design the policy**. The environment is not deterministic, it is **probabilistic**. 

The policy $\pi$ is expressed as the following:$$\pi(s,a)=\Pr(a=a|s=s)$$, or colloquially, the probability of taking action a, given the state s. This is probabilistic because this is a probability of choosing this action for a policy. For a traditional control systems, the rules never change and the action given the state is deterministic. 

Using **learning how to play Chess** as an example. Because we have the environment which is not deterministic, with the adversarial agent (other player) trying to beat the agent, which introduces randomness to the environment. So the agent playing Chess may have a policy to move a pawn in certain way 80% of the time in the given state, and 20% of the time in another state.

Once the agent has a policy, we have a probability model of taking an action given a state, so we can run that policy and see how much reward we get. And this happens over the course of some time. We take actions at time step 1 2 3 ... and measure state at time step 1 2 3... and there are rewards that we could be getting at each of these actions, but most often very sparsely. For a game of chess, we may have the reward function at the end if we win. But because there are so many actions we have made until that point, and maybe we were very close to winning until the last fatal mistake. Do we throw away the whole game because of a single mistake at the end? How do you figure out which actions were good and which actions were bad - designing this value function is extremely difficult optimisation problem and this is the heart of Reinforcement Learning. 

Part of designing a good policy is to understand what is the value of being in a certain state $\mathcal{S}$, given that policy $\pi$. Once we choose a policy we start to learn what is the value of each state of the system - each board position in chess, based on the expected reward I will get in the future if I start a the state and enact the policy, to pick and perform an action.
$$V_\pi(s) = \mathbb{E}(\sum_t\gamma^{t}r_t|s_0=s)$$
Here the gamma is the discount rate, as discussed in [[Markov Decision Process]]. We slightly discount the future rewards compared to my immediate rewards. $\gamma$ is a constant between 0-1, how much you favour getting the reward now versus sometime in future. 

Every combination of possible chess position is too large to hold in memory and calculate. What we aim to do with reinforcement learning is to **try and capture the rules of thumb** of what are good board positions. For example, in the game of Chess, you would probably have a better chance of winning if you keep your queen alive as long as possible. We will have a better expected value of getting a reward, this could be a rudimentary value function to be used. As we learn more about how to get the value, we will be able to **refine this value function** and get a better idea of of what matters in the game, which in turn allows us to **optimise the policy further**.

The goal is to **optimise policy to maximise future reward**.

We model the environment as a [[Markov Decision Process]]. As we established earlier, our environment is not deterministic. We think of our envrionment as being stochastic, which is the MDP. If we are in a state $S$ now, and I take an action $a$, there is some probability of me going to a new state at the next time step. This is part of what makes the optimisation of the policies so difficult because the environment is probabilistic. 

## Credit Assignment Problem
Very hard to tell what action sequence was actually responsible for getting the reward. Rewards are often very sparse. If the rewards were denser, we can learn faster, but with sparse rewards, then reinforcement learning is **sample inefficient**. We will have to play many times, have tons of examples to try learn the sparse rewards, very hard to learn what the right policy is. 
### Reward Shaping
Even if we have a single ultimate reward at the end like the game of Chess, we can try to increase the density of the reward by builiding a **proxy reward** so that the agent has more feedback to learn what are good and bad actions.

Almost all problems in control theory and machine learning are optimisation problem - ML you solve it through data, in controls you solve it through the dynamic models and constraints. 

- Differential Programming
- Monte Carlo
- Temporal Differencce
- Bellman Optimisation in 1957
- Exploration vs Exploitation
	- How much do I focus on optimising an existing strategy vs how much to try a new strategy.
- Policy Iteration
	- Iteartively update your policy to update your policy based on the new information from the environment. 
- Gradient Descent, Evolutionary Optimsiation, Simulated Annealing.
- [[Q-Learning]] - Q function is a function of state and action - what is the quality of being in that state and taking the action. Value function of the state and the action, assuming I do the smartest things in the future. Nice way of combining a policy and reward. 
- [[Hindsight Replay]] - Instead of throwing out all the data that did not result in reward, maybe my set of action would result in reward for a different value function. Replay the sequence of actions that didn't result in reward for this exact reward function.
