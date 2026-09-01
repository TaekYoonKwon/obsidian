Two bedrocks of reinforcement learning;
Policy Function: $\pi(s,a)=\Pr(a=a|s=s)$
Value Function: $V_\pi(s)=\mathbb{E}(\sum_t\gamma^tr_t|s_0=s)$
The problem typically is that for the value function you need to come up with the function that maps each individual states to a value, but for any moderately sized problem, the combination of all possible states explodes. 

In Dynamic programming, we assume we know the model of the environment. The entire goal is to **optimise your policy to maximise future rewards**, with a reward function designed in an unknown model of the environment.

# Model Based Reinforcement Learning
If you have **a good model of the environment** - [[Markov Decision Process]] or some differential equation, then this is a model based reinforcement learning. We assume we have a model of the reward and state, how they propagate, or formally: $$\begin{align}R(s',s,a)=\Pr(r_{k+1}|s_{k+1}=s',s_k=s,a_k=a)\\P(s',s,a)=\Pr(s_{k+1}=s',s_k=s,a_k=a)\end{align}$$ which is a formal way of saying, we have a model for quantifying the reward for the next time step, given state and action, and also a model for the policy for picking the action based on the current state and action.  

## Model as Markov Decision Process
If there is a specified **probability** of moving from state s to next state, given action a, and the probability is known, then we can use **policy iteration** and **value iteration**. Iteratively walk through the MDP taking actions, and assessing the value, then refining the policy function and the value function. This is solved using [[Dynamic Programming]] and it is proved by [[Bellman Optimality]]. See [[Actor-Critic Method]]
## Model as Nonlinear Dynamics
For deterministic systems - this is more of a continuous control problem. Optimal linear controls like LQR, Kalman filters are special cases of this **optimal non-linear control problem** with [[Hamilton Jacobi Bellman Equation]]. Solving this in dynamic programming usually becomes brute force search based. Usually not scalable to high dimensional systems. HJB has limitation on how many DOFs it can operate on. See [[Deep MPC]] 
# Model Free Reinforcement Learning
Most of the time we don't have a good model of our system. (eg. in chess I cannot write down my opponent as a MDP or differential equations). What Model free RL does is it approximates the dynamic programming, simultaneously learning dynamics and learning to update the policy and value functions without having the model.
## Gradient Free
If I can parameterise the policy by some variables and if we know what the dependency with those variables are, we may be able to take the gradient of the reward function with respect to the parameters to speed up the optimisation. But often times we cannot express the value function as a partial derivative of a parameter - maybe there exists but it is difficult to define a mathematical expression which governs the value function as a differential of some parameter. 
### Off Policy
We perform random moves sometimes to experiment, knowing that our value and policy function currently are suboptimal. The insight we gain from this random process might be really valuable. Key relevant techniques include [[Q-Learning]]. Quality function which denotes Q is the joint value of being in a particular state, and taking a particular action.
This is also important for [[Imitation Learning]]. See [[Deep Quality Neural Network]]
### On Policy (try hard mode)
If I am playing a number of chess games, and we are trying to learn the optimal policy and value function. For On Policy, we always try to do our best with the given value function and the policy, knowing that they may be suboptimal.
Key relevant techniques include [[Temporal Difference]], [[Monte Carlo Learning]], [[SARSA Algorithm]].
## Gradient Based
Update the parameters of the policy or the value function or the Q function directly using gradient optimisation. If we can sum up all of the future rewards and it is a function of the parameter theta that parameterise my policy, then we can use the gradient optimisation, things like [[Gauss-Newton Method]] and steepest descent. See [[Deep Policy Network]]