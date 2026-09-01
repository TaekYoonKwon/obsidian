$$0=\min_u[l(x,u)+\frac{\partial J^*}{\partial x}f(x,u)]$$
# Anatomy
- x and u: respectively denote continuous state vector (x) and the continuous control input vector (u)
- l(x,u): denotes the immediate running cost
- $\frac{\partial{J^*}}{\partial{x}}$: the partial derivative of the **optimal** cost-to-go function. Geometrically this is the slope of the costmap.
- $f(x,u)$: the continuous system dynamics. By definition this is the time derivative of the state vector (x).

>[!WARNING] Dimensionality
>I initially thought the running cost must be in state space dimension, but actually it's just how we visualise it, and from the true cost-to-go function calculation, we can see that $$J^*(x)=\int_0^\infty l(x,u)dt$$ and the integral with respect to time clearly suggest the running cost has the units of a time-rate (cost/time) 
>Therefore we must add with not $\frac{\partial{J^*}}{\partial{x}}$, but $\frac{dJ^*}{dt}$, for which we multiply by the system dynamics. 

Derived from [[Bellman Optimality]], Hamilton Jacobi Bellman (HJB) equation is the continuous-time optimal control proof. 

Essentially, the formula says, if you take actions u, which minimises the sum of l(x,u), the current running cost and the time derivative of the optimal cost, it should equal, provided we in fact have taken the actually optimal input. The key insight here is, with J*, we assume we already know the optimal cost map. So when we take an action and we look at the current running cost, if it doesn't match our expected drop of the slope of the cost for the optimal cost, either our optimal cost map is wrong or the action we took is suboptimal. 
## Optimal Policy
$$\pi^*(x)=\arg\min_u[l(x,u)+\frac{\partial{J^*}}{\partial{x}}f(x,u)]$$
Here, the policy is almost identical to the optimality condition, but instead of $min_u$, which is an operator which selects the **u that minimises the whole function** and returns the **minimum function output**, $\arg\min_u$ instead picks the same u, and return the u. 

# HJB Sufficiency Theorem
We can use the HJB equation to verify that the control is indeed optimal. For any policy to claim optimality, it will have to satisfy the optimal policy equation above and HJB equation simultaneously. Essentially, prove that your policy is optimal and when you used that claimed "optimal policy" throughout your system, your immediate cost to go never drops faster or slower than the projected total cost-to-go. Because to say there is a mismatch in the rate of change even when you have applied the optimal policy would mean either the policy is wrong or the optimal cost-to-go function is in fact not optimal. 

There are few more boundary conditions to claim optimality.. @todo
