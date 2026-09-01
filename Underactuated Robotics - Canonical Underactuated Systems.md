two link arm. Not a simple double pendulum as the link themselves have inertia. The goal is to have both the links stand upright. Actuator between the two links, but not at the either end of the links. Really difficult to control.

Equations of Motion:
$$M(q)\ddot{q}+C(q,\dot{q})\dot{q}=\tau_g(q)rBu$$$$q=\begin{bmatrix}\theta_1 \\ \theta_2\end{bmatrix}, u=\begin{bmatrix}\tau_{elbow}\end{bmatrix},B=\begin{bmatrix}0 \\ 1\end{bmatrix}, |u|\leq u_{max}$$
## Cart Pole
mass on a pendulum on a cart. Goal is to swing up and balance the pendulum.

Equations of Motion:
$$M(q)\ddot{q}+C(q,\dot{q})\dot{q}=\tau_g(q)rBu$$
$$q=\begin{bmatrix} x_{cart} \\ \theta_{pole}\end{bmatrix}
,u=\begin{bmatrix}f_{cart}\end{bmatrix}, B=\begin{bmatrix}1 \\ 0 \end{bmatrix},|u|\leq u_{max}, |x| \leq x_{max}$$

# Methodology to Achieve the Goal
Tabular Value iteration for discrete systems. It can use up to 5~6 dimensions. In both of the examples above, they are 4 dimensional, for 2D position and 2D velocity. We "can", but the amount of resolution needed is very high. 
>[!INFO] Challenges with tabular value iteartions for the problems in practice
> For most systems, there will be some "high sensitivity" area where small adjustments reacting to small errors are crucial to achieving the goal. For Acrobot and Cart pole, it will be near the goal position. 
> There has been attempts to try and increase the resolutions just around the sensitive area, and this is easy to do if the optimal policy is known before hand. But otherwise introduces many problems, such as increasing resolution where it doesn't need it and end up becoming slow for no reason. 

Will LQR work? LQR needs the system to be linear, which the two problems above are not. But there is still a way around it, we can use a coarse controller to handle the non linear dynamics from the swing up, then use LQR for near the top. This is a great solution for balancing near the top.
We must linearise the dynamics near the top. But how is it actually done?
## LQR for non-linear systems
$$\dot{x}=f(x,u)$$
LQR was $\dot{x}=Ax+Bu$. What we will do is to take the Taylor approximation around some nominal operating point. 
1. Choose $x_0,u_0$. Two arbitrary points defined around the problem operating condition. Ideally fixed points. 
2. $\dot{x}\approx f(x_0,u_0)+\frac{\partial{f}}{\partial{x}}_{|x=x_0}(x-x_0)+\frac{\partial{f}}{\partial{u}}_{|u=u_0}(u-u_0)$ 
>[!INFO] The expression above is taylor approximation. it is also commonly written as $x=f(x_0,u_0)+\text{higher order terms}$ 

Here f is a matrix, x and u are vectors. Hence $\frac{\partial{f}}{\partial{x}} \ \text{and} \ \frac{\partial{f}}{\partial{u}}$ are A and B matrices respectively. The expression $f(x_0,u_0)$ is equivalent to $\dot{x_0}$. 
>[!INFO] $f(x_0, u_0) = \dot{x_0}$??
> At a first glance it may seem like $\dot{x_0}$ should be the partial derivative, but look again.. 
> the dynamics $f(x_0, u_0)$ describes the state velocity by definition and is the pure time derivative at nominal state x and u which is what the $\dot{x_0}$ denote. 

We also express $\bar{x}=x-x_0$ and hence $\bar{\dot{x}}=\dot{x}-\dot{x_0}$. Then we can rearrange and establish the following equation: $$\begin{align}
\dot{x}\approx f(x_0,u_0)+\frac{\partial{f}}{\partial{x}}_{|x=x_0}(x-x_0)+\frac{\partial{f}}{\partial{u}}_{|u=u_0}(u-u_0) \\ 
\dot{x}-f(x_0,u_0) \approx \frac{\partial{f}}{\partial{x}}_{|x=x_0}(x-x_0)+\frac{\partial{f}}{\partial{u}}_{|u=u_0}(u-u_0) \\
\dot{\bar{x}} \approx \frac{\partial{f}}{\partial{x}}_{|x=x_0}(x-x_0)+\frac{\partial{f}}{\partial{u}}_{|u=u_0}(u-u_0)
\end{align}$$
And now we have the expression for $\dot{\bar{x}}=A\bar{x}+B\bar{u}$

### Example: Simple Pendulum
EOM: $ml^2\ddot\theta + b\dot\theta+mgl\sin\theta=\tau$
$q=\begin{bmatrix}\theta\end{bmatrix},x=\begin{bmatrix}\theta \\ \dot{\theta}\end{bmatrix}, u=\begin{bmatrix}\tau\end{bmatrix}$
Now we linearise about $\theta=\pi, \dot{\theta}=0, u=0$
These dynamics in state space form:
$\dot{x}=\begin{bmatrix}\dot{\theta} \\ \frac{1}{ml^2}[\tau-b\dot{\theta}-mgl\sin{\theta}]\end{bmatrix}$ 
and $\dot{x} =0$ we are linearising about a fixed point. 
The linearisation of this - the Jacobian matrix looks like:
$$\frac{\partial f}{\partial x} = \left.\begin{bmatrix}0 & 1 \\ -\frac{g}{\cos\theta} & -\frac{b}{ml^2}\end{bmatrix}\right|_{\begin{align}\theta=\pi\\\cos(\pi)=1\end{align}}= \begin{bmatrix}0 & 1 \\ -\frac{g}{l} & -\frac{b}{ml^2}\end{bmatrix}=A$$
$$\frac{\partial{f}}{\partial{u}}=\begin{bmatrix}0 \\ \frac{1}{ml^2}\end{bmatrix}=B$$
To simplify the problem, we say l = 1, m = 1, b = 0. Then we can express:
$$A=\begin{bmatrix}0 & 1 \\ 10 & 0\end{bmatrix}, B = \begin{bmatrix}0 \\ 1\end{bmatrix}$$
Is this a good approximation of pendulum near the top? For a linear system, the stability of the systems is all about the eigenvalues of the A matrix. 
$Av=\lambda v$ $\det(A-\lambda I)=0$, the eigenvalues and corresponding eigenvectors are: $\lambda_1=\sqrt{10}, v_1= \begin{bmatrix}1 \\ \sqrt{10}\end{bmatrix}$ and $\lambda_2=-\sqrt{10}, v_2 =\begin{bmatrix}-1 \\ \sqrt{10}\end{bmatrix}$ 

To draw the phase portrait from here:
x axis is theta, y axis is theta dot. Then you can just draw the line along the eigenvector? Because by definition, along the eigenvector there is no rotation. 

>[!NOTE] Linearisation
>we are linearising a non-linear system. The phase portrait shows that the linearisation still preserves a lot of the dynamics and is highly representative of the non-linear system. Usually, proving that the linearised system is stable, is usually enough to prove that the system is locally stable around the fixed point. 
> This is also true of the closed loop system. If we put a linear controller with a non-linear system, and we linearise the closed loop system about a fixed point, we can do the same stability analysis.

>[!WARNING] Marginal Stability of linearised system
>If a linearised system is marginally stable, it gives us no guarantee about the stability of the underlying non-linear system.
>


## Stabilisability and Controllability
Imagine we have a system $\dot{x}=Ax+Bu$
Is (A,B) controllable? and is (A,B) stabilizable? 
Systems can be underactuated but still controllable. 
>[!INFO] Clarification around semantics
>**Underactuation** is whether we are able to provide instantenous acceleration in all dimensions and I can take any arbitrary trajectory.
>**Controllability** is whether we are able to get to a new state space, given time. 
>These are different questions.
>**Stablisability** is whether as time goes to infinity, x(t) limits to 0.
>

LQR only needs to be stablisable. A system that is stable but has no control is stablisable. If you have no control authority but the system is already stable, it will tend to 0, hence stablisable. The reason is that if there are some axis of dynamics that are uncontrollable but stable, LQR does not need to control that axis, and only concern with unstable axis.  

