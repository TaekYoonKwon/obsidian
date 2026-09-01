There are two approaches to modelling geometry for motion planning - **Boundary representation** and **Solid representation**. 
For a boundary representation, to represent the earth, we might write the equation of a sphere. For a solid representation, we would describe the set of all points that are contained in the sphere. 

We define the world $\mathcal{W}$, There are two kinds of entities to be represented:
1. **Obstacle**: portions of the world that are permanently occupied.
2. **Robot**: Bodies that are modeled geometrically and are controllable via a motion plan

Both entities are subsets of $\mathcal{W}$, with the obstacle region represented as $\mathcal{O}$. This is conceptually easy to understand, but how do we actually represent this in a manner that is expressive and computationally efficient? 

## 1.1 Polygonal and Polyhedral Models
Polygonal and Polyhedral models are *solid* representations, developed in terms of a combination of *primitives*. Each *primitive* represents a subset of $\mathcal{W}$, that is easy to represent and manipulate in a computer. Think of basis polynomials, building blocks that allow us to represent much more complicated.

### 1.1.1 Convex Polygons
Consider $\mathcal{O}$, for the case when the obstacle region is convex, polygonal subset of 2D $\mathcal{W}$. Convexity is when you have interpolation between $x_1$ and $x_2$, any two arbitrary points on $\mathcal{O}$ always lie inside $\mathcal{O}$. 

A boundary representation of $\mathcal{O}$ is an **m-sided polygons**, described using **vertices and edges**. Every *vertex* corresponds to a "corner" of the polygon, and every *edge* corresponds to a line segment between a pair of vertices. For example:
$$(x_1, y_1), (x_2, y_2),\cdots,(x_m, y_m),$$where each vertex describes a corner of a polygon. 

A solid representation of $\mathcal{O}$ is an **intersection of m half-planes**. Half plane can define an edge, which is expressed in the form $f(x,y) = ax+by+c$, which draws a straight line, and divides the world in binary fashion, either bigger than $f(x,y)$ or smaller. 
We can easily derive these half planes from the vertices. Just take the line equation that corresponds to the edge from $(x_m, y_m)$ to $(x_{i+1}, y_{i+1})$. Then a half-plane, $H$ can be defined as a subset of $\mathcal{W}$:
$$H_i=\set{(x,y)\in \mathcal{W} \space \vert \space f_i(x,y) \leq 0}$$
Then the solid representation is just an intersection of all these half-planes:
$$\mathcal{O}=H_1\cap H_2 \cap H_3 \cap \cdots \cap H_m$$
### 1.1.2 Non-Convex Polygons
Most of the time, convexity requirement to represent an obstacle is too constraining. Now suppose $\mathcal{O}$ is a non-convex, polygonal subset of $\mathcal{W}$. In this case it can be expressed as:
$$\mathcal{O} = \mathcal{O}_1 \cup \mathcal{O}_2 \cup \mathcal{O}_3 \cup \cdots \cup \mathcal{O}_m$$
in which each $\mathcal{O}_i$ is a convex, polygonal set that is expressed in terms of half-planes as per the solid representation of a convex polygon.
Note for more complicated shapes, it can be defined in terms of any finite combination of unions, intersections and set differences of primitives. Also, there is no unique representation of non-convex polygons. Care must be taken to optimise computational performance in whatever algorithm that may be used to decompose a non-convex shape, but this optimisation is often an  [[NP-Hard Problem]].

#### 1.1.2.1 Define a logical predicate
A predicate is a Boolean-valued function, which evaluates to $\phi : \mathcal{W} \rightarrow \set{TRUE, FALSE}$, which returns TRUE for a point in $\mathcal{W}$  that lies in $\mathcal{O}$ and FALSE otherwise. It is basically a collision detector, that returns TRUE if the point is occupied by an obstacle, FALSE otherwise.

For a convex obstacle, the predicate can be defined by the following logical conjunction:
$$\alpha(x,y) = e_1(x,y)\wedge e_2(x,y)\wedge \cdots \wedge e_m(x,y)$$
This basically says, predicate $\alpha$ returns TRUE for the given point x and y, if and only if the point satisifies all of the half-planes. This is easy for convex obstacle, as convexity allows us to naively stack logical AND ($\wedge$).

For a non-convex obstacle, it is more complicated. Because we cannot naively assume the point is included in the obstacle just from looking at the list of half-planes. The heuristics and their validity depends on the way the non-convex obstacle is expressed, but for the expression above which is a logical intersections of convex obstacles, we can say:
$$\phi(x,y) = \alpha_1(x,y)\vee \alpha_2(x,y)\vee \cdots \alpha_n(x,y) $$
Which are logical OR($\vee$) of convex obstacles. If any point is included in any of the convex obstacle, it is deemed to be in the obstacle. 

### 1.1.3 Polyhedral Models
For 3D world, the polygonal model can be nicely generalised, by replacing polygons with polyhedra and replacing half-plane primitives with half-space. 

A **Boundary representation** of a polyhedral model is defined by three features: 
- Vertices - forms a boundary between three or more edges
- Edges - forms a boundary between two faces
- Faces - flat polygon embedded in $\mathbb{R}$.
To efficiently store the boundary representation, one can use the [[Doubly Connected Edge List]]. 
A **Solid representation** of a polyhedral model on the other hand, can be constructed from the vertices. Each face of $\mathcal{O}$ has at least three vertices along its boundary. Assuming these vertices are not **collinear**, an equation of the plane that passes through them can be determined:
$$ax+by+cz+d=0$$
where $a,b,c,d\in \mathbb{R}$ are constants. Similar to half-plane representation, half-space representation can be constructed as following:
$$f(x,y,z)=ax+by+cz+d$$
$$H_i=\set{(x,y,z)\in\mathcal{W}\space\vert\space f_i(x,y,z)\leq 0}$$
Unlike the polygonal representation, care must be taken in how to represent the polyhedron. Half-edge data structure (which is just a directed edge), stores for each face, the list of edges that form the face's boundary in counterclockwise order. For every edge, the arrows point in opposite directions, as required by the half-edge data structure. 

The equation for each face is as following:
- Choose three consecutive vertices, $p_1, p_2, p_3$, in counterclockwise order on the boundary of the face.
- Then the cross product $v = v_{12}\times v_{23}$ always yields a **vector that point out of the polyhedron**, normal to the face. 

Same principles as the polygons apply in deriving the predicate. 

## 1.2 Semi-Algebraic Models
Unlike the polygonal and polyhedral model, which had f as a linear function, semi-algebraic model has f as any polynomial with real-valued coefficients and variables x and y (x, y, z for 3D). Polygonal and Polyhedral are both algebraic as well, they are just restricted to using a first-degree polynomials. 

A point set determined by a single polynomial primitive is an *algebraic set* and a point set expressed by a set of logical operators (unions and intersections) of algebrac sets is *semi-algebraic* sets.

For solid representation, the primitive from polyhedrons still hold:
$$H_i=\set{(x,y)\in \mathcal{W} \space \vert \space f_i(x,y) \leq 0}$$
With the only difference being $f_i$ can now a non-linear function. such as:
$$f=x^2+y^2-4$$
