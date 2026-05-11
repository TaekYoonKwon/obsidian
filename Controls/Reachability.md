## Definition
If $\xi \in \mathbb{R}^n$  is reachable, that means there is some prescribed input u, that will yield $$\xi = \int_0^te^{A(t-\tau)}Bu(\tau)d\tau$$
From [[Cayley-Hamilton Theorem]], we can expand this $e^{A(t-\tau)}$ term.
$$\xi = \int_0^t(\phi_0(t-\tau)Bu(\tau).I+\phi_1(t-\tau)u(\tau)AB+\phi_2(t-\tau)u(\tau)A^2B\cdots d\tau$$
Take out the A and B from the integral, since A and B do not vary according to time. 
$$= B\int_0^t \phi(t-\tau)u(\tau)d\tau+AB\int_0^t \phi_1(t-\tau)u(\tau)dt\cdots +A^{n-1}B\int_0^t\phi_{n-1}(t-\tau)u(\tau)dt$$
This can be put into matrix form, which is the controllability matrix multiplied by convolution integrals:
$$\begin{bmatrix}B & AB & \cdots & A^{n-1}B\end{bmatrix}\begin{bmatrix}\int\phi_0(t-\tau)u(\tau)d\tau \\\int\phi_1(t-\tau)u(\tau)d\tau \\ \vdots \\ \int\phi_{n-1}(t-\tau)u(\tau)d\tau  \end{bmatrix}$$
