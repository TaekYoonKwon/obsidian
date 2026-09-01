Quality Learning, is a branch of [[Reinforcement Learning]], and this method doesn't rely on a pre-established model the world or the value of the model for its learning. 

# Quality Function
Jointly taking an action and state, the Quality (action-state) is able to fuse an **"action in a specific state"**, which allows us to look at the immediate value of a decision before the **stochastic environment** forces us into a s'.
>[!INFORMATION] Distinction With Value Function
>**Value function** from [[Dynamic Programming]] describes what the value of being in a current state is, assuming you take the **best action** from now.
> **Quality function** on the other hand, describes what the quality of being in this current state is, **for any action** that the agent may take.
> This is coined as Joint Quality function, that we take into account both the action and the state. 

Quality function is a more integrated representation of the [[Markov Decision Process]] than separate action and state. 

To formulate the mathematical expression, we can write$$\begin{align}\begin{split}Q(s,a)&=\mathbb{E}(\mathcal{R}(s',s,a)+\gamma V(s')) 
\\ &=\sum_{s'}P(s'|s,a)(\mathcal{R}(s',s,a)+\gamma V(s'))
\end{split}\end{align}$$which reads, the quality function is the expected future rewards given a state s and action a, the sum of immediate rewards for the next state s', and the discounted quality of the future states from s'.

Here the stochastic of the environment is the reason we express the quality as a summation of probabilities. There is a probabilistic element of the state transition, even for the same action, same state.
> [!WARNING] Distinction with Policy/Value Iteration
> This is actually the same expectation value for the value function for **policy iteration** for [[Dynamic Programming]]. 
> The key difference is that **for policy/value iteration**, you require a **model of the transition matrix** for the future, so that you could simulate and adjust the value function/policy.  
> But in the quality function, once we start learning Q(s,a), the information about how the environment evolves through actions are **implicitly encoded in the quality function**, so we do not need a model. 
> This is the key feature which allows us to optimise for the policy **"Model-free"**


Once we learn a good quality function through optimisation, we can extract the optimal value function and the optimal policy like so:
$$\begin{align}\begin{split}&V(s)=\max_aQ^*(s,a) \\ &\pi^*(s,a)=argmax_aQ^*(s,a) \end{split}\end{align}$$so our value function for the state is the quality function for the action which maximises the quality function, and the policy is picking the action which maximises the quality function for every state.

# Quality Function Update
Q-Learning takes the idea from [[Temporal Difference Learning]], which iteratively updates the value function throughout the episode based on the rewards and we get to pick how far in the past we want to credit the rewards to. The big difference is that instead of iteratively refining the value function, we refine the quality function. The quality function update can be expressed as:$$Q^{new}(s_k,a_k)=Q^{old}(s_k,a_k)+\alpha(r_k+\gamma\max_aQ(s_{k+1},a)-Q^{old}(s_k,a_k))$$which is basically the same as the value function update from TD Learning. For this, what we are doing is essentially scale the TD error (innovation of how much reward we got vs how much we expected) with the existing estimate of the quality for that action and state, scaled by the learning factor $\alpha$.

Q-learning is an **off-policy TD Learning of the Q function**, which means for TD Target estimate, $R_\sum=r_k+\gamma\max_aQ(s_{k+1},a)$, we are free to choose an immediate action that may not be our optimal policy, but in adding the **cumulative quality of the next state, we must estimate this using the optimal policy**. Without the $\max_{a}$ condition, we are letting the algorithm stop looking for the theoretical optimal path and shifts to evaluating our actual, flawed behaviour. 
This off-policy nature of Q-learning is what allows it to learn from a lot of different types of experience, so not just what you believe is the best, because your optimal policy is based on a potentially sub-optimal quality function. 
> [!WARNING] Comparison to On-policy Algorithm
> Compared to the On-policy algorithm like **SARSA** below, the TD Target estimate for off-policy learning is $\max_aQ(s_{k+1},a)$ which is looking at the quality of the next step given we take an action a. So this allows us to decouple the algorithm's policy from learning. This action can come from anyone. 
> On the other hand, on-policy algorithm has TD Target estimate which involves $a_{k+1}$, which uses our own perhaps incomplete quality function and hence policy. So it is impossible to decouple the source of action and the agent.  

## SARSA: State Action Reward State Action 
SARSA is essentially, **on-policy TD Learning of the Q function** , and our **TD Target Estimate**, resembles the following: $$R_\sum=r_k+\gamma Q^{old}(s_{k+1},a_{k+1})$$and note here the $\max_a$ term is gone for the on-policy learning, and we replace it with $Q^{old}$, the old quality function which was evaluated, with assuming you take the optimal actions at every timesteps according to the optimal policy. 

The quality function update is expressed as:$$Q^{new}(s_k,a_k)=Q^{old}(s_k,a_k)+\alpha(r_k+\gamma Q^{old}(s_{k+1},a_{k+1})-Q^{old}(s_k,a_k)$$

This mandates that we pick the optimal action every timestep. If we don't pick the best possible action at every timestep, it will degrade our quality estimate.
