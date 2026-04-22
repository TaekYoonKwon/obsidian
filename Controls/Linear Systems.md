# 1. The State Space Representation
A continuous linear system is represented as the differential equation:
$\dot{x} = Ax + Bu$
- _A_: The Dynamics Matrix (N x N), it describes the internal physics and how the state variables are coupled to each other.
- _x_: the state vector, $x \in R^N$.
- _B_: The Actuator/Input Matrix
- _u_: the control input vector 
# 2. The Analytical Solution & The Taylor Series
If we integrate the uncontrolled differential equation($\dot{x}=Ax$), we get 
$$x(t) = e^{At} . x(0)$$This is an amazing starting point, because with this representation, we can preserve the state regarding A over time, through differentiation.
This result can be verified using Taylor Series. 
$$e^{At} = I + A.t + \frac{A^2.t^2}{2!} + \frac{A^3.t^3}{3!} ...$$ 
**The Computational Bottleneck:** But this is not practical for computing as we cannot reiterate this forever. So it is more practical to think of the system in eigenvalues and eigenvectors, in eigenspace. 
# 3. The Eigenspace
The conversion to eigenspace is helpful because while it doesn't help us get rid of the infinitely recursive nature ([[Cayley Hamilton Theorem]] helps us with that) it does help us with dealing with exponents of A. When we decompose the matrix A to eigenspace:
$$A = V \Lambda V^{-1}$$ where: $$V = \begin{bmatrix} v_1 & v_2 & v_3\end{bmatrix}$$ $$ \Lambda = \begin{bmatrix} \lambda_1 & 0 & 0 \\ 0 & \lambda_2 & 0 \\ 0 & 0 & \lambda_3 \end{bmatrix}$$
- V is the eigenvector matrix
- $\Lambda$ is the diagonal matrix of eigenvalues.
# 4. Collapsing the infinite series
If we substitute this definition to the Taylor series expansion, all the intermediate $V$ and $V^{-1}$ terms cancel each other out:
$$e^{At} = V(I + \Lambda t + \frac{\Lambda ^ 2 t ^2}{2!} + \frac{\Lambda ^ 3 t^3}{3!}... )V^{-1}$$
Because we know $\Lambda$ is a diagonal matrix, raising it to a power just means raising its individual scalar components to that power. This means the infinite polynomial inside the parentheses collapse, and because we know the infinite series is the definition of $e^{\Lambda t}$:
$$ e^{\Lambda t} = \begin{bmatrix} e^{\lambda_1 t} & 0 & 0 \\ 0 & e^{\lambda_2 t} & 0 \\ 0 & 0 & e^{\lambda_3 t} \end{bmatrix}$$
So we may express the original Taylor Series as:
$$e^{At} = Ve^{\Lambda t}V^{-1}$$
