To understand non-linear dynamics, we frequently use a single pendulum as an example. Most of the concepts from non-linear dynamics can be inferred from analysing a single pendulum. Consider the following pendulum:
![[Pasted image 20260509134508.png]]
Following the [[Lagrangian Derivation of EOM]], the EOM for this system is:
$$ml^2\ddot{\theta}(t)+mgl\sin\theta(t)=Q$$ Where Q is the generalised force. The LHS is F-ma=0.
If we consider a general input which models a damping torque (from friction):$$Q=-b\dot{\theta}(t)+u(t)$$
## 1. Nonlinear Dynamics with Constant Torque
Take a torque, Q that does not vary with time for now, for the sake of simplicity, our EOM becomes:$$\begin{gather*}ml^2\ddot{\theta}(t)+mgl\sin\theta(t)=-b\dot{\theta}(t)+u_0\\\rightarrow ml^2\ddot{\theta}(t)+b\dot{\theta}(t)+mgl\sin\theta(t)=-u_0\end{gather*}$$
Now that we have our EOM, if it was a typical linear system, we would just be able to solve this differential equation to get an expression for $\theta(t)$. But this is difficult to do for a non-linear system. This is because of the following reasons:
1. Linear systems have "general solution", such as $e^{\lambda t}$, that allows us to superpose the solutions, allowing us to decompose a big problem to smaller chunks. Non-linear problems don't hold superposition.
2. Laplace Transform does not work for a function such as sin. 
3. With constant torque, we can use Jacobi eliptic functions, but that's some advanced math, and with damping and non-linear input introduced, a general closed-form analytical solution does not exist.

If we care about the long term behaviour of the system, we can use graphical method to try and tackle this problem. Basically the question we are trying to answer is, given initial condition, x(0), what is the behaviour of x as time approaches infinity: $x(\infty)$ 
### 1.1 Overdamped systems:
If our system is heavily damped to the point where it dominates the LHS, we can express the EOM as: $$ml^2\ddot{\theta}(t)+b\dot{\theta}(t)\approx b\dot\theta=u_0-mgl\sin\theta(t)$$
For now, we ignore the manifold, and ignore that $\theta$ wraps around on itself every $2\pi$. Then the equation becomes $b\dot\theta=u_0-mgl\sin x(t)$
So the system becomes approximately first order.

For now we consider the system with no input, $u_0=0$.
If we plot the phase portrait for this system, we reveal that it is like a minus sine wave. 

![[Pasted image 20260509142806.png]]
Observations:
- The system has a number of **fixed points** or **steady states**. When $\dot{x}=0$, our system is stationary.
- If our system starts in one of those fixed points, it will **never leave that state**
- For the second fixed point $(x=\pi)$, if the system starts little bit to the right $(x=\pi+\epsilon)$, then we have a positive $\dot{x}$, which creates a positive feedback loop, away from the fixed point
- This second fixed point is **unstable**.
- For the first fixed point (x=0), system starting little bit to the left or right of it will happily move back to the fixed point. We call this **locally stable**.

There are multiple types of stability:
1. *Locally stable in the sense of Lyapunov* - a fixed point x* is stable in the sense of Lyapunov, if there exists some boundary in which if the system started within that boundary, it may accelerate initially but it is **guaranteed** to never leave this boundary, but also **guaranteed to never converge** to the fixed point.
2. *Locally attractive* - A fixed point is locally attractive if for every $\epsilon$, where $\epsilon$ denotes a small displacement, which implies if the starting position was a small displacement away from the fixed point, as time approaches infinity, the trajectory will converge to the fixed point. **DOES NOT GUARANTEE LOCALLY STABLE**
3. *Locally asymptotically stable* - Locally stable in the sense of Lyapunov && locally attractive. The system will **eventually converge**.
4. *Locally exponentially stable* - If we can define a boundary in terms of displacement, that if the starting position was $x^*+\epsilon$, that the error will not exceed some exponential function: $||x(t)-x^*||<Ce^{-\alpha t}$, for some positive constant C and $\alpha$. The system will **converge at a bounded rate**
5. *Unstable* - Not Locally stable in the sense of Lyapunov.

As we can see, first order one-dimensional system on a line will either monotonically approach a fixed point or monotonically move towards $\pm\infty$. Oscillations are impossible.

> [!INFORMATION] In many cases, the dynamics are parameterised. What this means is there is a tuning knob, which controls the location of the stability points. This is called **Bifurcation**. 

If we move back to the manifold - from x to $\theta$, then we find that around our unstable points, we may misinterpret it as the system is oscillatory. The phase portrait has to be plotted on a cylinder. 

## Phase Portrait
A pendulum, as evident by the governing equation of motion, is a second order system: $$ml^2\ddot{\theta} +mgl\sin\theta+b\dot{\theta}=u_0$$ But dealing with second order system is difficult. It would be much easier to represent this as a **coupled two dimensional first order system** as the following: $$\begin{align}\dot{x_1}=x_2 \\ \dot{x_2}=f(x_1,x_2)\end{align}$$where $x_1=\theta$ and $x_2=\dot{\theta}$. With this coupled first order dynamics, we can graphically represent the coupling using **Phase Portrait**
![[Pasted image 20260606174053.png]]
This is for a pendulum without any damping. This is why we see the vector never getting smaller and "come back to the same spot" around the equilibrium. In reality, with damping and friction it looks more like this:
![[Pasted image 20260606174341.png]]

## Feedback Cancellation
Imagine we choose controller $u = 2mgl\sin(\theta)$. This changes the whole vector field (Phase portrait), by moving over the equilibrium to be in the middle, where $\theta=\pi$. Remember from the phase portrait.
We don't actually have the ability to affect $\theta \text{ or } \dot{\theta}$. We can only affect $\ddot{\theta}$. This means we cannot physically move the vector fields, we can only affect the direction and size of the vectors, and even then, only by a limited amount because we have limited torque. We cannot also make the vector travel backwards from what they already do, because that would be violating physics. 
The maximum torque that we need for our controller is 2mgl. Even for a simple pendulum, rewriting the vector field and moving the center is not trivial. 