# Configuration Space
Most motion planning algorithms do not solve for paths directly in the physical world. Instead they operate entirely within C-space (Configuration Space), which is a mathematical set of all possible positions and orientations a robot can assume - [[Motion Planning - Rigid Body Transformation]].

In physics and control theory, C-space is deeply coupled with Lagrangian mechanics, as it allows dynamics to be expressed using the precise degrees of freedom of a body. The C-space in physics is called Lie group. The Lie group algebra and calculus is quite complicated. Luckily in Motion Planning, C-space requires no calculus, as the C-space is treated as a [[Topological Manifold]].

A specific robot **configuration** $q=(x_t, y_t, \theta)$ dictates its exact placement, and mapping from local to world coordinate uses a standard 3x3 homogenous transformation matrix, which is just 2x2 rotation + translation vector. 
$$\begin{bmatrix}x' \\ y' \\ 1 \end{bmatrix} = \begin{bmatrix}cos(\theta) & -sin(\theta) & x_t \\ sin(\theta) & cos(\theta) & y_t \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}$$
Where $x'$ and $y'$ are in world coordinate. 

The set of all configuration space $q=(x_t, y_t, \theta)$ is clearly a subset of $\mathbb{R}^3$, but because $\theta$ wraps around, we write that $\mathcal{C}=\mathbb{R}^2\times\mathcal{S}^1$, which expresses that the configuration space is a combination of Euclidean space and a circle (wrap-around), a 3D manifold.
### 3D Configuration space
The 3D configuration space has 3 axis of rotation that wraps around from 0 to 2$\pi$. instead of the Euler angle, we use quarternion, and the configuration space is 6D manifold - $R^3 \times RP^3$. 
- [ ] Fully populate this section of the note

# Problem Statement
We express the world in configuration space. Then we have part of configuration space that is prohibited due to collision. We define $A(q) \subset \mathcal{W}$, which denotes a closed set of points in the world occupied by the robot, when it transformed to configuration q. 

A configuration q places the robot into collision iff $$\mathcal{A}(q)\cap\mathcal{O}\neq \emptyset$$This is just a formal way of saying, if our robot in configuration space, intersects with an obstacle in the configuration space, we say we have collided. So suppose we have the robot and obstacle trying to occupy at least one common point. 

The set of all non-colliding configurations (which is just the inverse of the collision) is called the free space: $$\mathcal{C}_{free}=\set{q\in\mathcal{C}\space \vert \space \mathcal{A}(q)\cap\mathcal{O}=\emptyset}$$
So in precise language, we can define the problem as:
> [!INFO] Given a robot description $\mathcal{A}$, an obstacle description $\mathcal{O}$, a C-space $\mathcal{C}$, an initial configuration $q_1\in \mathcal{C}$ and a goal configuration $q_G$, compute **a continuous path** $\tau : [0,1]\rightarrow \mathcal{C}$, with $\tau(0)=q_1$ and $\tau(1) = q_G$. 

> [!NOTE] Note the path must be continuous; otherwise, the robot would appear to “teleport” from one place to another, which is obviously cheating. Gradual motions through $\mathcal{C}$ make the robot move gradually through $\mathcal{W}$. 

# Five different schools of thought..
Although our problem statement clearly states **continuous**, the reality is our hardware and hence computation is not continuous. So we must discretise the problem or somehow keep our problem strictly in continuous space, which inspires three different schools of thoughts, and two new breeds.
## 1. [[Combinatorial Planning]]

## 2. [[Sampling Based Planning]]

## 3. [[Search Based Planning]]

## 4. [[Optimisation Based Planning]]

## 5. [[Reactive Method]]
