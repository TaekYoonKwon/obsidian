Break up the problem of finding the optimal policy and reward function for the reinforcement learning problem, so we think of what the optimal thing to do is for the next few steps. 

Start with the value function $$V_\pi(s)=\mathbb{E}(\sum_k\gamma^kr_k|s_0=s)$$which is the sum of expected value of the reward over time, with decay function gamma, to prioritise immediate reward.
If we drop the subscript, and describe "the value function":$$V(s)=\max_\pi\mathbb{E}(\sum_{k=0}^\infty\gamma^kr_k|s_0=s)$$ which is if we were to **pick the best possible policy $\pi$**, and if we always played that policy, the V would be **the value of being in a particular state** assuming we do the best possible thing at every point.

Bellman figured out we can take the sum of future rewards, break them up into current reward at timestep now, plus all of the rewards from the next timestep to the end. Refer to [[Bellman Optimality]]. The expression now become:$$V(s)=\max_\pi\mathbb{E}(r_0+\sum_{k=1}^\infty\gamma^kr_k|s_1=s')$$ or simplified even further:$$V(s)=\max_\pi\mathbb{E}(r_0+\gamma V(s'))$$ we can recursively define the value function.  The value function at my current state is the value function at the next timestep. If this is satisfied, we can optimise locally at every time step to locally optimise and when we stitch them together becomes the global optimal solution.

Dynamic Programming is how we solve, how to build up the value function, which becomes manageable when Bellman's optimality condition holds. 

## Top-down approach
List out all possible subproblems, try to solve all of them, recursively fill out the table of subproblems and the solutions, until finding the optimal solution.

## Bottom-up approach
Always start with the smallest problem, work your way up. start with the winning configuration, then optimise back, what would've been the best action that would've led me to this configuration, and recursively we can build up optimisation sequence.

# Value Iteration
Value iteration allows using [[Bellman Optimality]] condition to iteratively build a refined estimate of my value function through optimising a sequence of events through a known model. $$V(s)=\max_a\sum_{s'}P(s'|s,a)(R(s',s,a)+\gamma V(s'))$$ We are trying to pick the action that maximises the value for us - the immediate reward + sum of decaying future rewards.
>[!INFORMATION] The sum and probability component
>In this equation, we must express the value as a sum of probability, because it is a [[Bellman Optimality]] on [[Markov Decision Process]]. 
>MDP fundamentally expresses the reward as an expected value, which we evaluate as weighted average based on probability, and Bellman equation is a sum of recursive reward value.
>

This assumes that I know the model of where I will be (s') in the next time step for an action a, and also the value of every state V(s'). This value map may be inaccurate, but since that is the best way to evaluate the value of a sequence of action that we have, to work with this flawed value function, we try to find the best sequence of actions to maximise the reward for this value function. And then we update the value of the current state, for the table of value to state map. Then I take the next step, do the process again. And we iteratively update the value function. Even if the values were initialised with poor values, the value function gets better, and iteratively we refine the value function. Eventually, our value functions will arrive to convergence where it is close enough to optimality, and our policy will be derived based on that.

# Policy Iteration
We lock in a policy first, then iteratively update the value function, until the value function converges. With this policy now known and refined, we look for a better policy that will yield a better reward given this current value map. We pick the best policy, then repeat the prior step. So that given a state s, we can take the best action a, that maximises my future rewards. This requires that I have a good model of the value function for this current state and future states, which we refine iteratively as we go. 

The mathematical expression for the value function given a policy is as follows:$$\begin{align}\begin{split}V_\pi(s)& =\mathbb{E}(\mathcal{R}(s',s,\pi(s))+\gamma V_\pi(s'))
\\ & = \sum_{s'}P(s'|s,\pi(s))(\mathcal{R}(s',s,\pi(s))+\gamma V_\pi(s'))
\end{split}\end{align}$$which roughly reads as, the value of our state for the current policy is the expected value of the immediate reward + discounted value of the future state for our policy (MDP), and the expected value can be extracted, being equal to the sum of each probability for next state and its corresponding value from MDP.  

The mathematical expression for a policy is as follows:$$\pi(s)=argmax_a\mathbb{E}(\mathcal{R}(s',s,a)+\gamma V_\pi(s'))$$which roughly reads as our policy given a state is to choose an action a which maximises the expected value of the immediate reward (leading to s', the next state which has the biggest reward) in addition to considering the value of the next state with decay component to prioritise the immediate reward.

In short, policy iteration helps us to figure out what the best policy is that will maximise the given value function, and the value iteration allows us to refine our value function for every state, and in conjunction they work together to refine the value and policy closer to the optimality. 

It is impratical to use Value Iteration or Policy Iteration for any problem with large state space.

Key observation here is that policy and value are intrinsically linked. We can introduce Quality function, which is the quality of being jointly in a state s and taking an action a. This leads us directly to [[Q-Learning]].