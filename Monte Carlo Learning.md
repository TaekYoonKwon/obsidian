When we don't have access to a model for how the world evolves or what the reward structure is, we are forced to use **trial and error approach**.

This is the **simplest approach** to model-free learning.

We first define the **cumulative reward function**. Monte Carlo learning is an *episodic learning algorithm* - it needs to run an entire episode of states and action sequence, before it can use the information for learning. Monte Carlo can be used for anything that has a definitive end. The cumulative reward function is defined as:$$R_\sum=\sum_{k=1}^n\gamma^kr_k$$which is roughly cumulative reward function is the sum of reward at all timesteps with discounting factor $\gamma$. 

You pick and enact some policy, then run through the game. And we compute the cumulative total rewards, discounted by the discounting factor $\gamma$, this is how we can prove optimality in some conditions, using $\gamma$ as a tuning knob for how much we care about the immediate reward. 

Based on this, the simplest thing to do is to take and divide up the reward equally among every state my system took on the way to the finish line. At the end of the episode, once we've computed the cumulative reward for how much reward I have accumulated, we adjust our value function:$$V^{new}(s_k)=V^{old}(s_k)+\frac{1}{n}(R_\sum-V^{old}(s_k))\space\space \forall k\in[1,\cdots,n]$$
So my new value function for every state, is equal to the old value function +  cumulative reward divided by the number of steps taken. 
Updating the quality function is basically the same:$$Q^{new}(s_k,a_k)=Q^{old}(s_k,a_k)+\frac{1}{n}(R_\sum-Q^{old}(s_k,a_k))\space\space \forall k\in[1,\cdots,n]$$ every action-state pair we saw along the episode gets updated again by the small amount, divided by the number of steps.

We are also taking the difference with the old value/quality function. This is because if we had a perfect value/quality function, the cumulative value function must be the same as the old function, hence no update.

>[!WARNING] Efficiency
>Typically Monte Carlo learning is very inefficient. You are assuming every single step you took along the victory is equally important (cumulative value simply divided by the number of steps).

