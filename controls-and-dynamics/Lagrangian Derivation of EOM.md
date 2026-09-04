For multiple degree of freedom systems, it is often more convenient to derive equations of motion from Lagrange's equations. Lagrangian adopts exactly the same physics principles as the Newton's Law. Newton's Law applies perfectly for particles, but if we were to calculate the equations of motion for a rigid body, we now have to calculate for thousands of particles or abstract it to a particle. 
# Derivation
We firstly start with Conservation equation, for a large number (N) of particles, in a **conservative force field**  using state vector $x$. We may express the total kinetic energy in the system as:$$T=\sum_{i=1}^{M}\frac{1}{2}m_i\dot{x_i}^2$$where M is the degree of freedom. So for every degree of freedom, we take the mass and the velocity of that component. For particles travelling only in one direction, only one $x_i$ is required, but for a particle in 3D, it has 3xN degrees of freedom.

> [!IMPORTANT] Remember conservative force field is the one where the energy is path independent. No inefficiencies and energy being lost to friction etc. 
> This is because we may only define the potential energy field, V(x) for a conservative force field. Lagrangian $L=T-V$, is fundamentally a conservation of energy between kinetic energy and potential energy.

And we recall the momentum is a derivative of the kinetic energy - $p=m\dot{x}=\frac{d}{d\dot{x}}\frac{1}{2}m\dot{x}^2$. This momentum is for a particular particle in the coordinate direction $\dot{x}$.
If we take the time derivative of momentum, we have the expression for force: $$\frac{d}{dt}(\frac{\partial T}{\partial \dot{x_i}})=m_i\ddot{x_i}=F_i$$
For a conservative force field, We can find the amount of force being acted on by the potential field (could be gravity, whatever), as the gradient of the potential field. 
$$F_i=-\frac{\partial V}{\partial x_i}$$
If we equate both expressions:
$$\frac{d}{dt}(\frac{\partial T}{\partial\dot{x_i}})=-\frac{\partial V}{\partial x_i}$$
Here, we recall that kinetic energy T is invariant of the state vector x, it is only in terms of $\dot x$, and that potential energy is invariant of the state vector $\dot x$, only the state vector itself. Formally expressed:$$\begin{align}\frac{\partial T}{\partial x_i}=0 &&and&&\frac{\partial V}{\partial \dot{x_i}}=0\end{align}$$

So we can inject these terms above into our expressions and have no effect, which allows us to express the equivalent expression like this:$$\frac{d}{dt}(\frac{\partial (T-V)}{\partial\dot{x_i}})-\frac{\partial (T-V)}{\partial x_i}=0$$ Which is the Lagrangian!!
$$\frac{d}{dt}(\frac{\partial L}{\partial\dot{x_i}})-\frac{\partial L}{\partial x_i}=0$$
So this is basically Newton's second law of motion ($F=ma$) directly applied in Lagrangian. 
# Example: Mass-Spring System
Consider a 1-DOF mass spring system:
![[Pasted image 20260509124614.png]]
The kinetic energy of this system is $$T=\frac{1}{2}m\dot{x}^2$$ and the potential is:
$$V=\frac{1}{2}kx^2$$
And the Lagrangian is:$$L=T-V=\frac{1}{2}m\dot{x}^2-\frac{1}{2}kx^2$$
We apply the Lagrangian EOM:
$$\frac{d}{dt}(m\dot{x})+kx=m\ddot{x}+kx=0$$
That was easy, clearly Lagrangian is overkill for this simple system.

## Multiple DOF Mass Spring system
![[Pasted image 20260509125220.png]]
This is a 2DOF system. We have two joints, each having one DOF. The number of springs for this configuration is 3. 
To derive Lagrangian for this system, bring the formula for kinetic energy from above:
$$T=\sum_{i=1}^{M}\frac{1}{2}m_i\dot{x_i}^2$$
Then we can find the expression for T like so: $$T=\frac{1}{2}m_1\dot{x}_1^2+\frac{1}{2}m_2\dot{x}_2^2$$
And likewise, for the potential energy:$$\begin{gather*}V=\sum_{i=1}^{M}\frac{1}{2}k(x_{ref}-x_1)^2=\frac{1}{2}k_1(0-x_1)^2+\frac{1}{2}k_2(x_1-x_2)^2+\frac{1}{2}k_3(x_2-0)^2\\=\frac{1}{2}k_1x_1^2+\frac{1}{2}k_2(x_1-x_2)^2+\frac{1}{2}k_3x_2^2\end{gather*}$$
Now apply Lagrange's equation to $L = T-V$, for each DOF:
$$\begin{gather*}\frac{d}{dt}(\frac{\partial L}{\partial{\dot{x_1}}})-\frac{\partial L}{\partial x_1}=0\\\frac{d}{dt}(\frac{\partial L}{\partial{\dot{x_2}}})-\frac{\partial L}{\partial x_2}=0\end{gather*}$$
Then we can obtain the governing equations like:
$$\begin{gather*}m1\frac{d^2x_1}{dt^2}=-k_1x_1+k_2(x_2-x_1) \\ m1\frac{d^2x_1}{dt^2}=-k_2(x_2-x_1)-k_3x_2 \end{gather*}$$

# General Coordinate Systems
Another significant advantage of Lagrangian as opposed to Newtonian is that leaving the cartesian coordinates and moving to a general coordinate systems becomes trivially easy.
Take polar coordinates for example, $x_1$ and $x_2$ given by $q=[r, \theta]$. The number of degrees of freedom remains consistent. Remarkably, the Lagrangian EOM equation from above stays exactly the same:$$\begin{gather*}\frac{d}{dt}(\frac{\partial L}{\partial{\dot{q_1}}})-\frac{\partial L}{\partial q_1}=0\\\frac{d}{dt}(\frac{\partial L}{\partial{\dot{q_2}}})-\frac{\partial L}{\partial q_2}=0\end{gather*}$$
Complicated proofs/derivations are there, but probably beyond the scope. This is the main takeaway.
## Example - Simple Pendulum
A pendulum is one DOF system, but it is more convenient to describe the position of the mass in cartesian coordiantes (x,y) and derive the Lagrangian in the polar angle $\theta$.
For the position of the mass at the end of the pendulum, we have:$$\begin{align}x_1=h_1\sin\theta_1 &&&& y_1=-h1\cos\theta_1\end{align}$$
So then the kinetic energy is:
$$T=\frac{1}{2}m_1(\dot{x}_1^2+\dot{y}_1^2)=\frac{1}{2}m_1h_1^2\dot{\theta_1^2}$$
Then the potential energy is:
$$V=-m_1gh_1\cos\theta$$
The Lagrangian is:$$L=T-V=\frac{1}{2}m_1h_1^2\dot{\theta_1^2}+m_1gh_1\cos\theta$$
Apply the EOM:$$\begin{gather*}\frac{d}{dt}(\frac{\partial L}{\partial{\dot{\theta_1}}})-\frac{\partial L}{\partial \theta_1}=0\\m_1h_1^2\ddot{\theta_1}+m_1gh\sin\theta_1=0\end{gather*}$$

