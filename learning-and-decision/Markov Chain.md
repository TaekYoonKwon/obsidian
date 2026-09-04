A stochastic process - **a collection of random variables over time**, $X_n$, can describe the state of the system over time, and we call it the **state space**.

In the case of a discrete-time stochastic process $X_h$, h takes non-negative integers as an index, we may have a special case, where the **future states of the process depend only on the current states**.
Mathematically, this is defined as: A discrete-time stochastic process $X_t$ is called a **Markov Chain** if $$P\{X_n=i_n|X_0=i_0,\cdots,X_{n-1}=i_{n-1}\}=\mathbb{P}\{X_n=i_n|X_{n-1}=i_{n-1}\}\coloneqq p(i,j)$$ in which $i_n$ belongs to the state space for all $n$.
> [!INFORMATION] Translation
> **The probability** that the state of our robot ($X_n$) ends up in state $i$ at the next time step $n$, **given the entire history** of our state from the beginning of time, 
> is **same** as the probability that we end up in state $i$ at the next time step when we calculate the probability **just based on the current state** $n-1$

The **transition matrix P** for the Markov chain is the N x N matrix whose (i,j) entry $P_{ij}$ is contribution of the **probability that the next state from the current state i, to the next state j in the next time step**. To adhere to **conservation of probability**, there are two rules attached to this transition matrix:
1. $0\leq P_{ij} \leq 1$
   Every single entry in the matrix must be a valid probability between 0 to 100%.
2. $\sum_{j=1}^NP_{ij}=1$
   Sum of transition matrix, probability for every possible state must add up to 1.0, otherwise we have a probability that we may not be in the possible state.

Because Markov Chain's next state depends purely on the current state, it can be described conveniently in linear algebra. And if we want to predict the state after n steps, it is simply the time step power of the transition matrix $P^n$. The probability that the state will be j after n steps is denoted $p_n(i,j)$.

**Invariant Probability Distribution** - if you have P, the transition matrix and also a *probability vector* $\vec n$, which stays the same after the transition, $\vec{n}$ is a left eigenvector of P. It just does not get stretched since the eigenvalue is 1. Such vector $\vec{n}$ is called **invariant probability distribution for P**.
## Communicating States
Because Markov Chain mandates that every state transition is **only dependent on the current state**, if you have a transition matrix $P^m$, for any number m > 0, if you find that $$p_m(i,j)>0$$ Then we may say the **i leads to j**, and if we have a **circular reachability**, we say **i and j communicate with each other**. A pairwise communicating states is called a *class*.