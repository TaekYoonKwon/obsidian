One of the most important advancement in machine learning. It highlights events that have given me rewards more recently. 

Say there is a state action sequence, and you have some reward provided along the way. It may not be too far fetched to say, the **events (rewards) that happened more recently** are somehow **related to the rewards I'm getting now**. 

There is a finite difference in time between the action (or state) and the actual expected reward. In [[Monte Carlo Learning]], we have to provide equal weightings and contribution for every action-state pair through the time, but Temporal Difference learning says there is a state-action pair not too long ago, which has causal effect on the current reward, and this is a tuning knob.

# Temporary Difference 0 - TD(0)
Remember the **value function** can be expressed as per [[Bellman Optimality]]:$$V(s_k)=\mathbb{E}(r_k+\gamma V(s_{k+1}))$$
What the Temporal Difference learning does different to [[Monte Carlo Learning]], is that it updates the value function at state $s_k$, and there is a correction term for the value function update:$$V^{new}(s_k)=V^{old}(s_k)+\alpha(r_k+\gamma V^{old}(s_{k+1})-V^{old}(s_k))$$with the key difference being that instead of taking the cumulative reward at the end of the episode, we take the "cumulative reward" at the current time step, as we take action step by step in real time. 
Instead of averaging over all of the episode, we take the **target estimate** (new estimate of the value at this current timestep or the cumulative reward equivalent): $r_k+\gamma V^{old}(s_{k+1})$. This is what we expect the value to be for the state $s_k$. And we take the difference with the old estimate $V^{old}(s_k)$, to get Temporal Difference Error term or **TD Error**:$$\text{TD ERROR}=(r_k+\gamma V^{old}(s_k+1))-V^{old}(s_k)$$In colloquial terms, TD Error is analogous to the **innovation of total long-term value** - how much we expected this reward would be based on our old value function, and how much reward it actually was. 
 
>[!WARNING] Temptation to Simplify...
>If you look closely, it may look as if things can cancel out. Since $\gamma V^{old}(s_{k+1})$ looks the same as the $\gamma V^{old}(s_{k+1})$ term from $-V^{old}(s_k)$
>The Temporal Difference error term may appear as if it reduces to $r_k - r_k^{old}$, but that's not the case. 
>Until the value function has been fully trained, the $\gamma V^{old}(s_{k+1})$ term is not fully updated, so it may lead to premature cancellation. 
>Basically, we are still training, so we have to take into account that some of these value entries may not be fully updated.

Here Temporal Difference 0, **TD(0) is looking at one time step only**. There is only one time step delay between the action and only the actions associated with that one delta t will get included in the value function update. So does this mean if we find one action in the sequence which has huge immediate reward value, only the immediate action 1 timestep before will see this value and update its function. We have to iteratively run more trials to update and propagate this reward to the value in the previous actions.

We can extend the timeline by **extending the value function** like so:$$V(s_k)=\mathbb{E}(r_k+\gamma r_{k+1}+\gamma^2 V(s_{k+2}))$$and the **value function update extends** as well: $$V^{new}(s_k)=V^{old}(s_k)+\alpha(r_k+\gamma r_{k+1}+\gamma^2V^{old}(s_{k+2})-V^{old}(s_k))$$for two steps expansion.

>[!INFORMATION] Extending the time horizon
>We can conveniently add more timesteps to this framework, to allow more effective reward propagation. Our cumulative reward will look like the following: $$\begin{align}\begin{split}
R_\sum^{(n)}&=r_k+\gamma r_{k+1}+\gamma^2r_{k+2}+\cdots+\gamma^n r_{k+n}+\gamma^{n+1}V(s_{k+n+1})\\&=\sum_{j=0}^n\gamma^jr_{k+j}+\gamma^{n+1}V(s_{k+n+1})
\end{split}\end{align}$$
  As we continue to expand this time horizon, if we include all of the timesteps from start to finish, it will be [[Monte Carlo Learning]].
  We must keep in mind, that as we expand the time horizon, you may be able to update the value function to convergence faster, but the **credit assignment problem** returns. 
  As we look further and further into the future to look at the reward, you start to lose the direct link between action and reward. 
  
## TD($\lambda$)
Often we don't know what the effective time horizon is. Maybe some problems, you can do TD(100), or even TD(1000), to speed up the learning process. But this can come at a cost of credit assignment problem, and we may penalise a series of perfectly good actions for the eventual failure at the task, for something that happened at the very last. 
So instead of picking a rigid number for this loosely physically grounded time window, we use TD($\lambda$)
The approach we take with TD($\lambda$) is that you compute the cumulative reward function for all possible timestep window simultaneously, then depending on your $\lambda$ value, the reward given at a timestep gets accredited to the past actions in reverse-chronological order, so more credit goes to recent actions. 
We formulate the cumulative reward function as such:$$R_\sum^\lambda=(1-\lambda)\sum_{k=1}^\infty\lambda^{n-1}R_\sum^{(n)}$$According to this definition, the cumulative reward function for a given timestep includes from start to finish, but you have $\lambda$ to tune how much you want the earlier actions to be credited. As $\lambda \rightarrow 1$, you get a formulation closer to [[Monte Carlo Learning]], where every actions throughout the time are credited the same. 