# 1. Motivation
- We are interested in what the system does as $t\rightarrow\infty$. 
- Stability is something inherent in the system - for an unactuated system. 
- With control, we can mathematically prove that a system is stable, and will not have positive feedback loop where a small displacement explodes to infinity.
---
# 2. Stability Analysis in Eigenspace 
- Recall from [[Eigenvalues & Eigenvectors]] - how we can represent a system in eigenspace, and remove the coupling and the equations of motion just reduce to 1D differential equation for each eigenvector.
- It is much advantageous to perform this analysis in eigenspace. With dynamic couplings in ordinary timespace, it is unclear whether each axis will positively feedback each other.
- By converting it to eigenspace, this coupling is already accounted for, and we just need to check the eigenvalues. 
- The timedomain solution for the system is as follows:$$\dot{x}=Ax = Te^{Dt}T^{-1}x(0)$$
- Where: D is a matrix diagonal matrix of eigenvalues. $$D = \begin {bmatrix}\lambda_1 & 0 & 0\\ 0 &\lambda_2  &  0 & \dots \\ 0 & 0 & \lambda_3 & \\ & \vdots & & \ddots \end{bmatrix}$$
- Because the matrix is diagonal and they are decoupled, a given system's stability over time is entirely dictated by the exponential decay or growth of $e^{\lambda t}$.
---
# 3. Simple Eigenvalue stability check
- Remember an eigenvalue may have two parts - real and imaginary.$$\lambda_1=a\pm bi$$
- So for our exponents:$$e^{\pm \lambda_1t}=e^{at}[cos(bt)\pm isin(bt)]$$
- The complex part describes oscillation about that eigenvector, and is always $\leq$ 1 in magnitude. The sine and cosine terms simply wave back and forth between -1 and 1. 
	- ==NOTE:== The fact that we are getting imaginary eigenvalues out of a purely real system is because these eigenvalues come in complex conjugates to effectively cancel out the imaginary part. 
	- This matches with the physical intuition that for an oscillation to occur, energy is bouncing back and forth between two distinct states.  
- Here if a $\geq$ 0, the system grows over time, as the system is continuously multiplied by the exponent of it as it propagates through time.
- Likewise, if a $\lt$ 0 the system decays over time.
- If a = 0, the system is marginally stable.
- This signifies, if all of the eigenvalues of a given system are negative, the system is stable.  
---
# 4. Discrete Time system
- A system is represented often mathematically represented in continuous terms *t*, but in computers, there is no such thing as a continuous system.
- We can represent our system in discrete time system as following:$$x_{k+1}=e^{A\Delta t}x_k$$$$x_{k+1}=\tilde{A}x_k$$ where $x_k = x(k\Delta t)$ - state variable x at the timestep $k\Delta t$.
- This describes a discrete update. if the system was at $x_k$ at time k, the system propagation in the next time step $x_{k+1}$ is described only by the matrix $\tilde A$.
- $\tilde A$ is related to A matrix in the continous time domain by:$$\tilde A = e^{A \Delta t}$$
- Given the initial condition $x_0$, we can compute how the initial condition propagates and hence future trajectory by recursively multiplying $\tilde A$. $x_1 = \tilde A x_0$ and  $x_2 = \tilde{A}x_1$ and so on...
## 4.1 Discrete Eigenvalues & Unit circle
- In taking the eigenvalue in the discrete time domain, we find that the eigenvalues are also wrapped in an exponential. $$\lambda_{continous} = a\pm bi$$$$\lambda_{discrete} = e^{(a+bi)\Delta t}$$
$$\lambda_{discrete} = e^{a\Delta t}.e^{ib\Delta t}$$
- Looking at this, as $e^{a\Delta t}$ is a purely real component, the eigenvalue in discrete time domain can be expressed as: ${\lambda = Re^{i\theta}}$
- To propagate the initial condition, remember we recursively multiplying $\tilde A$. $x_1 = \tilde A x_0$ and  $x_2 = \tilde{A}x_1$. 
- To see how this propagates, we convert the discrete system into eigenspace (where $z_k$ is the state in eigenspace). The recursive multiplication simplifies to isolated 1D updates $$z_{k+1,i} = Re^{i\theta}z_k$$
- This means if the magnitude of the complex number here is less than 1, the system is stable and otherwise not. 