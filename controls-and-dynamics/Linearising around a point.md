# 1. Context
The notation we were using before, it requires our system to be linear, of the form:
$$\dot{x}=f(x)\rightarrow\dot{x}=Ax$$
This means, if our system is non-linear (as most systems are) we must linearise our function around a point.

# 2. Finding Fixed (Equilibrium) Points
To linearise a system, we first need a point to anchor our math to. We usually linearise around an equilibrium point, $\bar{x}$, where the system is naturally at rest.
Find $\bar{x}$ such that: $f(\bar{x}) = 0$.
Take an inverted pendulum for example, this is the exact upside down point, where the state variable, $\theta$ is momentarily 0.

# 3. The Taylor Series Expansion
To understand how we turn a non-linear f(x) into a linear matrix A, we use the Taylor Series Expansion. We can approximate the non-linear function f(x) at any point near our equilibrium $\bar{x}$ using an infinite polynomial
$$f(x)=f(\bar{x})+\left. \frac{\partial f}{\partial x} \right|_{\bar{x}} (x - \bar{x})+ \frac{1}{2!}\left. \frac{\partial ^2f}{\partial x^2} \right|_{\bar{x}}(x-\bar{x})^2 + H.O.T$$
where H.O.T stands for higher order terms. 

Because we are only designing our controller to work _very close_ to the equilibrium point, the distance $(x-\bar{x})$ is a very small number. If you square or cube a tiny decimal, it becomes microscopically small. Therefore, we can safely neglect all the Higher Order Terms.
Furthermore,  because $\bar{x}$ is an equilibrium point, we know that $f(\bar{x}) = 0$.

The Taylor Series essentially collapses to a simple linear approximation:
$$f(x) \approx \left. \frac{\partial f}{\partial x} \right|_{\bar{x}}(x-\bar{x}) $$
# 4. Defining the Derivation State $(\delta x)$
To make this look like our standard $\dot{x} = Ax$ equation, we define a new state variable, $\delta x$, which represents our deviation from the equilibrium point. 
$$\delta x = x- \bar x$$
Because $\bar{x}$ is just a constant number, its derivative is zero. Therefore:
$$\dot{\delta x}=\dot{x} - 0=\dot{x}=f(x)$$
Substitute this back into our simplified Taylor Series:
$$\dot{\delta x} \approx \left. \frac{\partial f}{\partial x} \right|_{\bar{x}}\delta x$$
This is significant. We have managed to approximate a non linear system into a linear format, where our linear A matrix is exactly equal to the derivative of $f(x)$ evaluated at the equilibrium point.
# 5. The Jacobian Matrix
Because $f(x)$ is a system of multiple variables, taking the derivative $\frac{\partial f}{\partial x}$ means taking the partial derivative of every system equation with respect to every single state variable. This matrix is called the Jacobian matrix.
$$\left. \frac{Df}{Dx} \right|_{\bar{x}} = \begin{equation}\left[\frac{\partial f_i}{\partial x_i}\right]\end{equation}_{\bar{x}}$$
Let's say, for a system:
$$\dot{x_1}=f_1(x_1,x_2)=x_1 x_2$$
$$\dot{x_2}=f_2(x_1,x_2)=x_1^2+x_2^2$$
Then our Jacobian can be defined as:
$$\frac{Df}{Dx}=\begin{equation}\begin {bmatrix}\frac{\partial{f_1}}{\partial{x_1}} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1} & \frac{\partial f_2}{\partial x_2} \end{bmatrix}\end{equation} = \begin{bmatrix}x_2 & x_1 \\ 2x_1 & 2x_2 \end{bmatrix}  $$
To finish the linearization, you would simply plug the numeric values of your fixed point $\bar{x}$ into that final matrix, yielding a standard matrix of constants that you can pass.

# 5. Hartman-Grobman Theorem
This all sounds ideal, lineraising around a point with the assumption that as long as we don't deviate too far from the equilibrium point $(x-\bar{x})$, the higher order terms in the Taylor Series becomes small enough that we can ignore them. 
Hartman-Grobman theorem says:
- If the eigenvalue of the linearisation around the fixed point is hyperboic (all of the eigenvalue have non-zero real part), then the linearisation of the system describes the system with good accuracy.
- If any eigenvalue is purely imaginary, our linearisation is **not accurate.**
- Because according to our definition, and Euler representations of the typical eigenvalue: $$\lambda = \sigma + i\omega$$ where $\sigma$ is the real part, and $\omega$ is the imaginary part. The Taylor Series expansion of this represents the exponent of lambda as: $$e^{\lambda t}=e^{(\sigma+i\omega)t}=e^{\sigma t}(\cos(\omega t)\pm i\sin(\omega t)) $$
- if the real part is 0, it essentially means along that eigenvector, there is no decay nor amplification. This is structurally unstable. The energy must exchange with other axis at least.
- This indicates that the $x^2$ and $x^3$ and $H.O.T$ from the Jacobian derivation above, contains the dominant forces in the dynamic system.
- To analyze stability here, linear state-space fails, and we must use advanced non-linear techniques.