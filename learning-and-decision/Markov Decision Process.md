**Markov Decision Process**, like [[Markov Chain]], describes a sequence of events where the probability of each event depends only on the previous event.

# Definition
A Markov Decision Process is defined as a tuple $<\mathbb{S}, \mathbb{A},p,r>$, which consists of:
- a state space $\mathbb{S}$
- an action space $\mathbb{A}$
- transition probability function $p$, $\mathbb{S} \times \mathbb{A} \rightarrow \mathbb{R}$. 
	- Note the $\times$ denotes cartesian product - every possible combination. So **the output, a real number probability** requires **a state space input and an action state input**.
	- $p(s'|s,a)$ - This is the probability that the next state space will be s' given the current state *s* and current action space *a*.
- reward function r, $\mathbb{S} \times \mathbb{A} \rightarrow \mathbb{R}$.
	- Same as above, cartesian product
	- $r(s,a)$ is the cost of reward of taking action a in the state s.
> [!INFORMATION] Discrete MDP
> If the state space and action space is **discrete**, the Markov Decision Process is **Discrete Markov Decision Process**. 
> The transition probability function is $p_a(s,s')$. In most cases, MDP is continuous, since commands (actions) and state space are continuous in robotics. 
> Discrete MDP are mostly for example purpose.. and you operate on chessboard etc.

Now that we have a representation of the problem, we need a **mechanism to pick an action**, given a state. This mechanism is called the **policy**. Formally, we express it as a sequence of mappings $\phi_t$, time $t \in \mathbb{N}$, from each state $s \in \mathbb{S}$, to an action $a \in \mathbb{A}$.
A **Markov policy** is when you have a policy that depends only on the current state, or formally: $\phi_t\mathbb{S}\rightarrow \mathbb{A}$, such that $\phi_t(s)\in\mathbb{A}(s)$ for all $s\in\mathbb{S}$. 
If $\phi_t$ are independent of time t, such policy is called a **Stationary Markov Policy**.
> [!INFORMATION] Example 
> So if you have an agent, that hates to revisit where it was, you have non-markov policy because now you have a policy that depends on previous state. 
> If you had a agent that did not complain, have identical and consistent performance and had 0 preference, you would have a stationary markov policy.

# Bellman Expectation Equation
Standard Bellman Equation states a rule: $$v(s)=E[R_{t+1}+\gamma v(S_{t+1})|S_t=s]$$
This can be elaborated as: The **expected total value of my current state** $v_p(s)$ is **equal to my immediate reward** $R_{t+1}$ plus the **discounted value of wherever I ended up next**. Bellman Equation is important because it **decomposes the value function** into two parts: immediate reward and the dicounted value of next state.

We can construct a Bellman equation subject to some policy $p$.  $$v_p(s) = \mathbb{E}_p \left[ R_{t+1} + \gamma v_p(S_{t+1}) \mid S_t = s \right] \tag{1}$$ or in terms of both state and action pairs: $$v_p(s)=E_p[R_{t+1}+\gamma v(S_{t+1}, A_{t+1})|S_t=s, A_t=a]$$
We can use this result to find the state-value function and the state-action value function for a given MDP with a given policy $p$.

## Define the Value Function
So how do we define the "value" of a policy and hence find the optimal policy? We first define a scoreboard, $v(i,p)$, which is the utility function under policy $p$, and $i$ is the initial state.
Then the value vector $v$ of the utility function is $$v_*(i)\coloneqq \sup v(i,p), i\in\mathbb{S}$$This reads - **value vector** $v_*$ of the initial state defines the **theoretical maximum score over the lifetime for every possible policy**, given our initial state. The `sup` (supremum) means we find this ceiling by searching across every single possible policy $p$ in existence.

This sounds computationally impossible to do it brute force. So we formulate this as an optimisation problem.

We cannot have computer evaluate expected value, so we must convert this to a mathematical expression. 
$$\begin{align}
\begin{split}
v_p(s) & = E_p[R_{t+1}+\gamma v_p(S_{t+1})|S_t=s] 
\\ & =E_p[R_{t+1}|S_t=s]+\gamma E_p[v_p(S_{t+1})|S_t=s]
\\ & =\sum_{r\in\mathcal{R}}rp(r|s)+\gamma \sum_{r\in\mathcal{R}}\sum_{s'\in\mathbb{S}}\sum_{a\in\mathbb{A}}v_p(s')p(s'|s,r|a)\pi(a|s)
\\ & =\sum_{r\in\mathcal{R}}\sum_{s'\in\mathbb{S}}\sum_{a\in\mathbb{A}}r\pi(a|s)p(s'|s,r|a)+ \gamma\sum_{r\in\mathcal{R}}\sum_{s'\in\mathbb{S}}\sum_{a\in\mathbb{A}}v_p(s')p(s'|s,r|a)\pi(a|s)
\\ & = \sum_{a\in\mathbb{A}}\pi(a|s)\sum_{r\in\mathcal{R}}\sum_{s'\in\mathbb{S}}p()
\end{split}
\end{align}$$