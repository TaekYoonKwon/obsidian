# Introduction
Combinatorial approach to planning has thrived largely during 1980s, and the core idea is to simply **connect the dots** in the plane, with a curve that avoids obstacles. If we guarantee all the vertices live in free configuration space $\mathcal{C}_{free}$, and connecting the vertices to form edges, which will also live in free configuration space. 
# Step by Step Methods
The exact methodology differs per implementation, but the general workflow is as follows:
1. Decompose the set of free configuration space into a set of tripezoids, with the vertices on the obstacle's vertices. From each vertex, shoot rays upwards and downwards, until it hits another obstacle boundary. 
2. Place one vertex in the interior of every trapezoid, pick centroid for simplicity
3. Place one vertex in every vertical segment 
4. Connect each segment vertex to the two vertices that are in the interior of the neighbouring trapezoids. Each connection forms an edge in the graph.
![[Pasted image 20260505173623.png]]

Here the intermediate result from the steps above does not set the $q_1$ and $q_G$ as starting and finishing point respectively. A simple way to connect them up would be to find the trapezoid that contains $q_1$ and $q_G$ and connect them to the vertices in their respective trapezoids. 

# The Catch
Combinatorial method at a glance looks pretty nice - it constructs a *discrete representation* of the problem, which *exactly* captures the solution. There is no sampling error or approximation error here. These methods are complete, able to correctly identify in finite amount of time whether a solution exists or not. 
However, this is only nice for a robot modelled as points. If we consider a robot modelled as a polygon, then to figure out the exact collision points, each vertices for obstacle has to account for the geometry of the robot itself, which is a Minkowski Sum. If the robot or the obstacle is non-convex, we will have to do convex decomposition, then trapezoidal decomposition. 
Even worse, if you have a rotation, the configuration space for collision free - $C_{free}$ no longer has strictly linear boundaries - it now has sine and cosine to represent the boundary, as we interpolate from each vertex to form an edge. This is a semi-algebraic representation. 
That's not to say it is impossible - semi-algebraic combinatorial planning is a thing, but the number of facets generated to evaluate grows exponentially, even worse for 3D. Semi-algebraic combinatorial methods do exist (Schwartz-Sharir cylindrical decomposition, Canny's roadmap algorithm), but the number of cells/facets grows exponentially in the _dimension of C-space_ — doubly exponential for cylindrical decomposition, singly exponential for Canny.
This is why a planar robot with rotation (3 DOF) is already painful and a 3D rigid body (6 DOF) is essentially intractable in practice. These methods are rarely implemented; they exist mostly to establish that exact, complete algorithms are theoretically possible

# TLDR: Only practical for  $\leq$ 3DOF Problems..