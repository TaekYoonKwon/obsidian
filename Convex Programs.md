# Terminology
## Sets
### Cone
A set $K \subset \mathbb{R}^n$ is said to be a **cone**, if for all $x\in K$ and $y > 0$, we have $yx \in K$. 
This is just a fancy way of describing a set of numbers which can be stretched by an arbitrary amount and still be inside the set. So there is an infinity element to it, but with strict direction. 
### Convex
A line segment connecting any two points in $\mathcal{C}$  is also contained in $\mathcal{C}$. 
### Origin
$\mathbb{O}^n \coloneqq 0 \in \mathbb{R}^n$. Trivial example but stretching 0 anywhere (which you can't) and drawing a line from anywhere within 0 (which you can't) are still in 0...
### Non-negative Orthant
$\mathbb{R}^n_{\geq 0}\coloneqq x \in \mathbb{R}^n : x \geq 0$. This is just the physical quantities that cannot be negative. So imagine in 2D - color the whole top right quadrant.
### Second Order Cone (Lorentz Cone)
$\mathbb{L}_n \coloneqq (x,y) \in \mathbb{R}^n : ||{x}||_2\leq y$. Spatial magnitude of a vector x, cannot exceed some scalar limit y. It looks like a cone standing on its vertex in 2D vector x. So suppose the magnitude of the vector x cannot exceed some scalar y.
### Semidefinite Cone
$\mathbb{S}_n \coloneqq X \in \mathbb{R}^{n\times n} : X = X^T, a^TXa \geq 0$ for all $a \in \mathbb{R}^n$. 
To unpack this, imagine X as a transformation matrix, and a as a vector. If the transformed vector (Xa) has the dot product taken, ($a^TXa$), the dot product is positive. The new vector is pointing at roughly the same direction as the original vector. To satisfy this, the matrix X must have the diagonal elements positive and the determinant positive.
## Convex hull
The convex hull of a set $\mathcal{S} \in \mathbb{R}^n$ denoted as conv($\mathcal{S}$) as the smallest convex set that contains $\mathcal{S}$. It's like the smallest blanket that we draw over a irregular shape that includes all of the shape, to make it convex. 
## Polyhedron
A shape created with intersection of half-planes. They have all the edges in straight lines. Mathematically, they are expressed as: $\mathcal{P}\coloneqq x\in \mathbb{R}^n : Ax+b\geq 0$ 
### Polytope
Polytope is just a subsection of a Polyhedron, now with the guarantee that the shape does not stretch to infinity.
## Conic form
Most of the sets which we encounter in convex optimisation can be expressed in **conic form**$$\mathcal{C}\coloneqq\{x\in \mathbb{R}^n : Ax + b \in \mathcal{K} \}$$ for some matrix A, vector b, and closed convex cone $\mathcal{K}$. Depending on the definition of the convex cone $\mathcal{K}$, we can express many different geometry:
- $\mathcal{K} = \mathcal{O}^m$ (origin) => **Affine Space**
  Because this expression just reduces to $Ax+b = 0$, this is just point, straight line and flat plane 
- $\mathcal{K} = \mathbb{R}^m_{\geq 0}$ (Non-negative Orthant) => **Polyhedron**
  This reduces the expression to $Ax+b \geq 0$. Intersection of half planes, which is a polyhedron.
- $\mathcal{K} = \mathcal{L}^m$ (Second-order Cones) => **Convex quadratic sets**
  We are forcing the vectors to be bounded by scalar magnitude. This produces smooth curves, like spheres, ellipsoids, or paraboloids.
- $\mathcal{K}= \mathcal{S}^n$ (Semi-definite Cone) => **Spectrahedron**
  