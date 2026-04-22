# 1. Definition
$$A\xi = \lambda\xi$$
$$T = \begin{bmatrix}\xi_1 & \xi_2 & \xi_3&... \end{bmatrix} $$
$$D = \begin {bmatrix}\lambda_1 & 0 & 0\\ 0 &\lambda_2  &  0 & \dots \\ 0 & 0 & \lambda_3 & \\ & \vdots & & \ddots \end{bmatrix}$$
- $\xi$ is an eigenvector, where through this axis, any multiplication on the matrix only results in stretch or shrink, no rotation.
- $\lambda$ is an eigenvalue, which describes the amount they get stretched or shrunk.
# 2. Redefine the system in eigenspace
The whole point of converting the system to eigenspace is so that we can decouple the system. In time space, for a given system, all state vars are likely highly coupled, where input to one state severely affects others. This makes the system equation verbose. 
By putting it in eigenspace, we can effectively decouple the system, represent it as a collection of 1D problem.
1. First we diagonalise the dynamics matrix:
$$AT = T D \rightarrow T^{-1}AT=D$$
2. Next we convert the state vector to be in eigenspace. Let *z* be the state vector in **eigenspace**: 
$$x= Tz$$
3. Take the derivative from both sides, then substitute for *z*.
$$\dot{x}=T\dot{z}=Ax$$
$$\dot{z}=T^{-1}ATz$$
4. Also substitute our diagonal matrix D:
$$\dot{z}=Dz$$
5. This looks like:
$$\frac{d}{dt}\begin{bmatrix}z_1\\z_2\\z_3\\\vdots\end{bmatrix} = \begin{bmatrix}\lambda_1 & 0 & 0 & \dots \\ 0 & \lambda_2 & 0 & \dots \\ 0 & 0 & \lambda_3 & \dots \\ & \vdots & & \ddots\end{bmatrix}\begin{bmatrix} z_1 \\ z_2 \\ z_3 \\ \vdots\end{bmatrix}$$
This multiplies out to incredibly simple, independent 1D problems:
$$\dot{z_1} = \lambda_1z_1$$
$$\dot{z_2} = \lambda_2z_2$$
$$\dot{z_3} = \lambda_3z_3$$
Because this is a trivial 1D calculus, to solve this differential equation:
$$z(t) = e^{Dt}z(0)$$

# 3. Remarks
- Thinking of the system in the eigenspace makes it nice & simple. But at the end of the day, we are interested in how the system behaves in x state space, not z state space.
- We know the real world solution is:
$$x(t)=e^{At}x(0)$$
- Using our definition $A = TDT^{-1}$, we can plug into the Taylor Series expansion of $e^{At}$ to see how to convert back to time space.
$$e^At=I+At+\frac{A^2t^2}{2!}+...$$
$$=T.T^{-1}+TDT^{-1}t+\frac{TDT^{-1}TDT^{-1}}{2!}+...$$
$$=T[I+Dt+\frac{D^2t^2}{2!}+...]T^{-1}$$
$$e^{At}=Te^{Dt}T^{-1}$$
- WIth this $e^{At}$, one can easily perform analysis in eigenspace, then apply it back to the system doamin.
- The key is to always think in terms of eigenspace for system behaviours. Eigenvectors and eigenvalues reveal the coupling between state spaces, and dealing in eigenspace allows us to eliminate the coupling of the dynamics, which would make the system impossible to analyse and actuate. 