From Phase Portrait, we get an insight into how the system behaves in physics. The vector field is given by the physics and our system. The job of control is then to change the vector field for a desirable behaviour. 

# Problem Statement
Given the trajectory $x(),u()$ for $\forall t \space x(t)$ (the entire trajectory throughout $t \in [0,t_{max}]$) , Assign a score, single scalar number (reward or cost). Maybe for pendulum, how long it took for starting position to the goal position. We can apply **constraints**, for things like torque limits or $x(t_f)=x_{goal}$. We can only consider trajectories that satisfy these constraints. 

### Example: Minimum time problem for Double Integrator
$\ddot{q}=u \text{ subject to }|u|\leq 1$. 
Physically, we can think of a wood block on a track, with q being the position on the track. 
Goal: drive to $q=\dot{q}=0$ in minimum time (from any initial condition). 

First intuition for best possible is "bang-bang control" - non smooth control of applying extreme control inputs. We can find the "policy" aka control. 
For this control: $\ddot{q} = u, u=-1$, we examine how this affects the phase portrait. First integrate this control input: $\dot{q}(t) = \dot{q}(0)-t$ and $q(t)=q(0)+\dot{q}(0)-\frac{t^2}{2}$. This makes the portraits as such;
![[Pasted image 20260606181434.png]]
Blue line is for negative extreme input, and Red line positive extreme input. Here if we were to start at some arbitrary $q, \dot{q}$ we will have to take either the blue vector then take the red vector to gradually come down to the origin which is $q=0,\dot{q}=0$. But notice there are blue and red line that go through the origin; if we start at the initial condition along those lines, we can just apply one control to get to the origin and this is our **optimal control** for that initial condition. This is the Double Integrator **minimum-time policy**.

This is for a very simply double integrator, but how do we come up with general optimal control for a general system? We use **Dynamic Programming**.

Minimum time is often a preferred criteria against which we optimise. 
## Discrete Dynamic Programming
To represent the phase portrait, we can use discrete vertices and edges in graph representation. 
We have discrete states, $s_i \in S$ discrete actions $a_i \in A$ and discrete time $s[n+1]=f(s[n],a[n])$. 
> [!INFORMATION] *s* denotes states in discrete time, and *x* denotes states in continuous time

The cost of traversing some edge will be denoted as g(s,a), and total cost of some trajectory will be $\sum_ng(s,a)$. In continuous time, we just write integral instead of sum. 
>[!WARNING] The key idea here is this is an additive cost. We recursively add a cost to calculate the total cost. This makes solving for the optimal control easier.

For instance, for an objective to minimise the time to goal position, we can write: 
g(s,a) is 1 if $s\neq s_{goal}$ , 0 otherwise.   

## Dynamic Programming definition
Dynamic programming is a recursive algorithm which solves backwards from the goal.$$J^*(s_i)=\min_{a[\cdot]}\sum_{n=0}^\infty g(s[n],a[n])$$Here J* denotes the cost function to the goal which is a function of some state s, given the optimal actions $a[\cdot]$, the total cost we would accumulate.
The key idea in dynamic programming is that the optimal actions, $a[\cdot]$ is hard to find. With additive structure, it allows us to recursively define the J*, which has to start all the way from the goal.
$$J^*(s_1)=\min_{a_1}[g(s_1,a_1)+J^2(f(s,a))]$$
This is now searching over a single action in the time step. We can enumerate for all available actions at the current timestep. This is the condition to certify optimality, as well as the method to find the optimal control. 

If we start with random J, this is the algorithm:
$$\hat{J^*}(s_i)\leftarrow \min_a[g(s,a)+\hat{J^*}(f(s,a))]$$
where J hat* denotes the estimate of the optimal cost, and in programming sense, we can iteratively apply this [[Bellman Optimality]] resembling method to converge to the real J*. This is called **Value Iteration**.
If we can find an approximate cost to go function, we figured out reference for our actions.
# Cost-to-Go Function (J)
A textbook example, we have a grid world (discrete) and a robot in one of the cells, a goal point in one of the cells, and there is some obstacle which occupies a few cells which we must avoid. We are still in discrete world so the robot can only make discrete decisions too.
![[Pasted image 20260606185902.png]]
We want to find the cost to go function which will allow us to get to goal point in any initial condition. We can derive the goal function g(s,a), which is something like 0 at goal, 10 if in obstacle, and 1 in normal tile.
Basically, we go around updating every tile in iteration. Initially, the goal is 0 and every other tile accumulates a point, because of the state. Tiles that are not obstacle or goal would all get 1. Then next round, update every tile, normal tiles now get cost 2, but the tiles directly adjacent to the goal stays at 1. Then in the next iteration, we get every other tiles that are more than 2 tiles away from the goal, gets point 3 and so on. This is the value iteration update.
After x iterations, we end up with the full cost to go function. ![[Pasted image 20260606190617.png]]

>[!WARNING] In general, optimal policy is not unique, but the optimal cost to go is unique. 
>If we take optimal cost to go, and add some offset to it, the policy is still optimal

### Caveats with Discrete Dynamic Programming
- Accuracy - discretisation error
- Scalability, we cannot have good resolution if the problem space is big. **Curse of dimensionality**
- Model of the system must be known. If we have some uncertainty about the model, we can fit it using [[stochastic optimal control]]
- Cost function
- Accurate knowledge of the current state required. **Full State Feedback**
- We throw out a lot of the structure.
	- once we turn something into a graph, there is no similiarity between nodes and graph.

We take the limit of delta x and delta u, delta time as they approach 0.

## Reminder: Minimum Time Double Integrator
$$\ddot{q} = u, |u|\leq1$$
The cart on the 1D track. Optimal control is "bang-bang". We have a couple key trajectories with the cart system. The point at which the dynamic corresponds exactly to either slamming the brake as hard as possible or flooring the gas as hard as possible. 

The equation to reason about this system around the optimal cost to go function:
$s[n+1] = f(s[n], a[n])$, and the loss function is $\sum l(s[n], a[n])$, and optimal cost to go is $\forall s J^*(s)=\min_a[l(s,a)+J^*(f(s,a))]$.
>[!INFORMATION] For optimal cost to go:
>We can compute the quantity of J* for all actions, the minimum value obtained for the cost for this current state plus the projected cost in future. This is like the optimality condition. To call something optimal, the J equation and the pi equation has to match.

Optimal controller (optimal policy) is $\pi^*(s)=\arg\min_a[l(s,a)+J^*(t(s,a))]$.

>[!WARNING] Value vs action
>Similar to reinforcement learning and bellman optimality stuff, we separately calculate the minimum value and the policy for minimum, through value iteration in this given example. 
>The reason we need this value and policy definition is because our robots are underactuated, we must model the dynamics, and calculate the feasible trajectory and then we can figure out how to optimally get to our destination state. 

# Continuous Dynamic Programming
States are now $x$, actions are now $u$. time is $t$. The dynamic is now $\dot{x}(t)=f_c(x(t),u(t))$. The cost function is $\int_{t=0}^\infty l_c(x,u)$, the optimality condition was $\forall x 0=min_u[l_c(x,u)+\frac{\partial J^*}{\partial x}(f_c(x,u))]$. and the policy is $\pi^* =\arg\min_u[l_c(x,u)+\frac{\partial J^*}{\partial x}(f_c(x,u))]$. 
## Informal Derivation
$x[n+1]\approx x[n] + hf_c(x[n],u[n])$ - first order euler integration, h is the time step.
Similarly, discrete time version of cost is $l_d(x,u)\approx hl_c(x,u)$. Now if we take these values and put it into discrete cost to go equations:
$$J^*(x)=\min_u[hl_c(x,u)+J^*(x+hf_c(x,u))]$$How do we now approximate J*(x+hf_c(x,u))? we use partial derivative:$$J^*(x)=\min_u[hl_c(x,u)+J^*(x)+\frac{\partial J^*}{\partial x}hf_c(x,u)]$$
the J*(x) inside the min, doesn't actually depend on u, so we can actually take this out, and it will cancel with the J*(x) cancel each other out.$$0=\min_u[l_c(x,u)+\frac{\partial J^*}{\partial x}f_c(x,u)]$$Neither does h depend on u, so h can be removed too. 
Refer to [[Hamilton Jacobi Bellman Equation]]

## Infinitely-fine Grid World
$$\frac{dJ}{dt}= \frac{\partial{J}}{\partial{x}}f(x,u)$$
Here, f(x,u) is the system dynamics, which is essentially just a time derivative of the state vector, $\frac{dx}{dt}$. 
HJB says, that for optimal u, u*, $$\frac{dJ}{dt}=-l(x,u^*)$$Or in words, this means if you are following the policy, your cost-to-go score will go down at the rate of accumulating cost. The RHS is the instantenous running cost, and the LHS is the rate of change of cost-to-go. 

## Double Integrator with Quadratic Cost
$$\ddot{q}=u,\space\space\space l(x,u)=q^2+\dot{q}^2+u^2$$
And since we are penalising u, we drop u limit for now to simplify the problem. 
Now we will prove that the following controller is optimal controller:
$$\pi^*(x)=-q-\sqrt{3}\dot{q}$$
The optimal cost to go function is:$$J^*(x)=\sqrt{3}q^2+2q\dot{q}+\sqrt{3}\dot{q}^2$$
Note according to HJB sufficiency theorem, we don't even need to understand the derivation of the optimal policy and the cost to go function. We can just test against the optimality condition. 
Note the state vector for this system is $x=\begin{bmatrix}q & \dot{q}\end{bmatrix}$, Then as J is a scalar, the partial derivative of J with respect to x looks like: $\frac{\partial{J}}{\partial{x}}=\begin{bmatrix}\frac{\partial{J}}{\partial{q}} & \frac{\partial{J}}{\partial{\dot{q}}}\end{bmatrix}$
Then, the slope of total cost-to-go, one component of HJB sufficiency theorem is evaluated to: $$\frac{\partial{J}}{\partial{x}}f(x,u)=\begin{bmatrix}\frac{\partial{J}}{\partial{q}} & \frac{\partial{J}}{\partial{\dot{q}}}\end{bmatrix}\begin{bmatrix}\dot{q}\\ u\end{bmatrix}$$
Then we use the expression above evaluated to replace the total cost-to-go.
$$0=\min_u[q^2+\dot{q}^2+u^2+\frac{\partial{J}}{\partial{q}}\dot{q}+\frac{\partial{J}}{\partial{\dot{q}}}u]$$
where: $\frac{\partial{J}}{\partial{q}}=2\sqrt{3}q+2\dot{q}$ and $\frac{\partial{J}}{\partial{\dot{q}}}=2q+2\sqrt{3}\dot{q}$. And all terms cancel each other out.

There are two sides to proving optimality:
1. Prove that the controller is optimal (the u* actually minimises the cost-to-go function)
2. Show that the minimum is indeed 0.

For the second part, we look at the dominating term, $u^2$ and we can guess that the optimality equation will resemble a parabola as a function of u. Then we can look at where the derivative with respective to u is equal to 0, to find the minimum.
$$\frac{\partial{}}{\partial{u}}[q^2+\dot{q}^2+u^2+\frac{\partial{J}}{\partial{q}}\dot{q}+\frac{\partial{J}}{\partial{\dot{q}}}u]=2u+\frac{\partial{J}}{\partial{\dot{q}}}=0,u=-\frac{1}{2}\frac{\partial{J}}{\partial{\dot{q}}}=-\frac{1}{2}[2q+2\sqrt{3}\dot{q}]$$ And indeed, $u=-q-\sqrt{3}\dot{q}$, which is the optimal policy.

Rewrite the cost function:
$$J^*(x)=x^T\begin{bmatrix}\sqrt{3} & 1 \\ 1 & \sqrt{3}\end{bmatrix}x$$and if we were to plot this, we would have an elongated quadratic bowl. Look at eigenvalues and eigenvectors. ![[Pasted image 20260620180955.png]]
So this system has already learned its dynamics. Clearly, if we are on the blue region of the cost-to-go, which is lower cost, it is telling us, that the displacement is negative and our velocity is positive, so we are already heading the right direction, hence lower cost. But for the other quadrant, we have positive displacement and we have positive velocity, so we have to slow down then go back, which is a lot of cost.

This is an example of [[Linear Quadratic Regulator]] - LQR. 

Let's say we have a system:$$\begin{align}\dot{x}=Ax+Bu \end{align}$$and the cost function $$\begin{align*}l(x,u)&=x^TQx+u^TRu&&Q\geq0,R>0\end{align*}$$ and the cost-to-go function:$$\begin{align*}J=\int_0^\infty l(x,u) dt=x^TSx\end{align*}$$
For the LQR's cost-to-go function, we can compute the optimal u and S as a function of Q, R, A and B.
$$\begin{align*}0=\min_u[x^TQx+u^TRu+\frac{\partial{J}}{\partial{x}}(Ax+Bu)] \\
\frac{\partial{[x^TQx+u^TRu+\frac{\partial{J}}{\partial{x}}(Ax+Bu)]}}{\partial{u}}=0
\end{align*}$$
>[!INFO] Deriving the optimal policy definition
>$\frac{\partial{J}}{\partial{x}}=2x^TS$, from a simple derivative. Then we multiply this to the optimality equation above:
>$$0=\min_u[x^TQx+u^TRu+2x^TSAx+2x^TSBu]$$
>Since we are optimising for u, and picking the bottom of the parabola with respect to u, we isolate only the terms with u.
>$$\min_u[u^TRu+2x^TSBu]$$
>Then as we did above, take the point at which the derivative equals 0. Where this expression equals 0, is where the optimality holds.
>$$\min_u\frac{\partial{[u^TRu+2x^TSBu]}}{\partial{u}}=2Ru+2B^TSx=0$$
>Then by diving the expression by 2 and rearranging:
>$$u=-R^{-1}B^TSx$$

The derivative is 0 with respect to u is where the minimum is again, because this is also a quadratic function with respect to u. Then we get:$$u^*=-R^{-1}B^TSx=-Kx$$Note the terms c are all linear, so we represent it as K. 
So the optimal policy for LQR is$$\pi^*(x)=R^{-1}\times B^T\times -Sx$$
Each term has unique physical connection: 
- -Sx term is "trying to go downhill". This is a vector aligned to the steepest slope direction of the cost map.
- $B^T$ term constrains us for underactuated robots, and this is what constrains us to not be able to take the steepest slope from the -Sx term. This projects all resulting vectors to the directions that our actuators can actually command.
- $R^{-1}$ term scales the command based on the actuator cost.
## Riccati Equation
To find S:
$$0=Q-SBR^{-1}B^TS+A^TS+SA$$
Every element of this matrix has to be 0. How to solve this for S? 