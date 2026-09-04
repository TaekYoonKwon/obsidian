Let's say we have a system:$$\begin{align}\dot{x}=Ax+Bu \end{align}$$and the cost function $$\begin{align*}l(x,u)&=x^TQx+u^TRu&&Q\geq0,R>0\end{align*}$$ Where Q is a positive semi-definite matrix, and R is positive definite matrix. 
>[!INFO] Symmetry
>The operations we perform with the state cost matrix (Q) and control cost matrix (R) are always this quadratic form. Add to the fact that they are required to be positive semi-definite and positive definite respectively, the asymmetric parts of the matrix cancel themselves out during the vector multiplication, meaning an asymmetric matrix behaves identically to its symmetric counterpart. 
>We enforce all Q and R matrix be symmetrical, to remove potential ambiguity, easier operations and properties of symmetrical matrices such as real eigenvalues guarantee and orthogonality. 
>$$R=R^T, Q=Q^T$$


>[!WARNING] Definite Matrix
>Positive definite matrix means, for every possible non-zero vector u, the scalar output of the operation $u^TMu>0$. Geometrically, this creates a perfect upward curving bowl, centered exactly at the origin.
>Positive semi-definite matrix is if for every possible non-zero vector u, the scalar output of the operation $u^TMu\geq0$. Geometrically, this creates some valleys, where in certain directions the bowl stays flat, but mostly curves upwards, never downwards. 

The state cost Q, can be semi-definite because we may want to not penalise certain dimensions of the state, such as only penalising position and not velocity. But the control cost R, must be strictly definite, because that would imply there are some control inputs eigenvectors which has no cost, and the system will try to command infinite along that vector.

# Cost-to-go Function
Cost-to-go function is infinite horizon, like an integral of cost function (l) over the time horizon. For LQR, the cost-to-go function stays quadratic, just like the cost function. 
$$\begin{align*}J=\int_0^\infty l(x,u) dt=x^TSx\end{align*}$$
>[!INFO] Time Dependency 
>We can make the time horizon finite, then the cost-to-go function would be time-dependent
>With infinite time horizon, we must be careful to make sure that we can actually reach the goal. Otherwise we will infinitely incur cost.
>Intuitively our behaviour when the time horizon is infinite (no urgency) vs 10 minutes will be very different. Boundary effect will be introduced. 

And we take the derivative of the cost-to-go function with respect to x, $\frac{\partial{J}}{\partial{x}}=2x^TS$.