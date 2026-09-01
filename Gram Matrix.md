# Definition
Gram Matrix is a matrix which contains all pairwise inner product (dot products) of a set of vectors. 
$$G=A^TA=AA^T$$
If you are starting with vectors, we will typically package it up to a matrix A but the following is also possible:
$x=[a, b]$ and $y=[c,d]$, if you were to take the Gram matrix of this, you would go:
$$G=\begin{bmatrix}x.x & x.y \\ x.y & y.y\end{bmatrix}=\begin{bmatrix}a^2+b^2 & ac+bd \\ac+bd &c^2+d^2\end{bmatrix}$$
