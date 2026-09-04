Instead of finding a point that minimises the function (Differential Calculus), we want to find a function that minimises a function.

# Motivation
One of the application is path minimisation problem. If we have two points on the plane:
$$\begin{align}A=(x_1,y_1)&&& B=(x_2,y_2)\end{align}$$
Then to find the minimum length between the two - then we would just draw a straight line between them. Easy problem. This is us optimising for the distance.

What if we had a chain hanging between A and B? It will naturally sag under in a concave up shape like U, instead of staying in a convex shape like $\cap$. Why is this? It is because it minimises the potential energy.

So if our goal was to minimise energy - the path that uses minimum energy would form the U shape.

So let us now consider - The Brachistochrome problem. If I have a marble rolling down a slope from a to b, what is the shape of the curve from a to b that minimises the time spent falling, if it is subject to gravity?
> This is actually the key motivational problem that led the development of the Variational Calculus as a field.

Basically, the idea of Variational Calculus is that we are trying to find a path(described by a function) that minimises some sort of a function.

## Distance Minimisation
$$I=\int_{x_1}^{x_2}dS$$
where dS is the incremental path length. From Pythagoras Theorem, we know:
$$\begin{align}dS^2=dx^2+dy^2\\=dx^2(1+(\frac{dy}{dx})^2)\\\rightarrow dS=\sqrt{1+\frac{dy}{dx}^2}\end{align}$$
So the above equation becomes:
$$I=\int_{x_1}^{x_2}\sqrt{1+\frac{dy}{dx}^2}dx$$
## Minimise the time (Brachistorchrone Problem)
$$I=\int_{x_1}^{x_2}\frac{ds}{v}=\int_{x_1}^{x_2}\frac{\sqrt{1+\frac{dy}{dx}^2}}{v}dx$$
since time taken is just distance divided by the velocity. Then we remember that the velocity can be via conservation of energy: 
- Assume starting at rest at an initial height $y_1$:
	- Initial Kinetic energy = 0
	- Initial Potential energy = $mgy_1$
	- Final Kinetic energy = $\frac{1}{2}mv^2$
	- Final Potential energy = $mgy$
	- Conservation of energy = $KE_i+PE_i=KE_f+PE_f$
	- Substitute: $0+mgy=\frac{1}{2}mv^2+mgy$
	- Then rearrange for v: $v=\sqrt{2g(y_1-y))}$
And we can simplify the equation above:
$$I=\int_{x_1}^{x_2}\frac{{\sqrt{1+\frac{dy}{dx}^2}}}{\sqrt{2g(y_1-y)}}dx$$
## Minimise the Energy - Catenary Problem
This is when we have a rope connecting two points and we are trying to find the length of the rope that connects the two points, that minimises the potential energy with the rope. 
$$I=\int_{x_1}^{x_2}\rho Agy\space dS=\rho Ag\int_{x_1}^{x_2}y\sqrt{1+\frac{dy}{dx}^2}dx$$
Length of the rope is given as:
$$l=\int_{x_1}^{x_2}\sqrt{1+\frac{dy}{dx}^2}dx$$
# Minimise the Lagrangian
We have the quantity L=T-V, The Lagrangian which is the difference between Kinetic and Potential. In moving from one time to another, a system will follow a path of stationary points with respect to the Lagrangian. At any given time, it will follow a path that minimises the Lagrangian. The function L here is the kinetic minus the potential energy. 
$$I=\int_{x_1}^{x_2}L[x,y,\dot{y}]\space dx$$
I is a functional because it is a function of a function. I is a function of y and its derivatives, which are functions. Basically, since we are minimising L, which is Kinetic energy - potential energy, this fundamentally means we penalise ourselves for using kinetic energy, and award ourselves for having high potential energy for every instance through the path (integral).

# The Idea
If we have points $(x_1, y_1)$ and $(x_2,y_2)$, and an optimal path from these two points that minimise some integral, and call the path $y(x)$, and we also draw a suboptimal path $\eta(x)$, that also starts and end at the same points as $y(x)$. Then we define the scale of suboptimality, $\epsilon$ - the scale at which the suboptimal path $\eta(x)$ is longer than $y(x)$ by, and the actual difference would be $\epsilon\eta$. This actual difference value to the optimal path, is the variation of y.
Basically we are checking against numerous theoretical paths that could be the solution.
> [!WARNING] $\eta$ is twice differentiable continuous.

So for a collection of arbitrary path $\bar{y}$:
$$\bar{y}(x)=y(x)+\epsilon\eta(x)$$
This $\epsilon$ and $\eta$ are considered the variations. These functions are arbitrary variations of y but it must satisfy the initial conditions $\eta(x_1)=\eta(x_2)=0$, so no variations at the start and end. Remember, this y describes a path. 
Now if we take the derivative of $\bar{y}(x)$ with resepect to $\epsilon$:
$$\frac{\partial\bar{y}}{\partial\epsilon}=\eta(x)$$
> [!WARNING] $\bar{y}(x)$ is not a single function, but an infinite family of paths. y(x) is the perfect path, $\eta(x)$ is one "wobble" shape, and $\epsilon$ is a slider that determines how much imperfection to introduce for that imperfect path.
>  As $\epsilon$ gets bigger, you have a path that diverges further from the optimal, and at $\epsilon$=0, we have the perfect path.

Then if we take the derivative now, we are looking at the slope of the path:
$${\bar{y}'}(x)=y'(x)+\epsilon\eta'(x)$$
Now if we take the derivative of $\bar{y}'(x)$ with respect to $\epsilon$:
$$\begin{equation}\frac{\partial\bar{y}'}{\partial\epsilon}=\eta'(x)\end{equation}$$

Then remember our objective function that we are trying to optimise:
$$I=\int_{x_1}^{x_2}F[x,y(x),y'(x)]\space dx$$
To define the objective function, we need to know the path itself and the slope. Because remember what we are trying to do, we are trying to find the total cost, of following this curve. So F is the cost per inch, and we are integrating over x1 and x2, to find the total cost I.

If this path y is an optimum path, (an extremal), there has to be a stationary point. This derivative value must be zero. 
>[!INFO] This derivative is taken with respect to $\epsilon$, which is our tuning knob. In our arbitrary path $\bar{y}(x)=y(x)=\epsilon\eta(x)$, our $y(x)$ and $\eta(x)$ are fixed. 

$$\left.\frac{dI}{d\epsilon}\right|_{\epsilon=0}=\left.\frac{d}{d\epsilon}\right|_{\epsilon=0}\int_{x_1}^{x_2}F[x,\bar{y}(x),\bar{y}'(x)\space dx=0$$
Here we have swapped y with $\bar{y}$. our definition of y is a theoretical perfect path, that does not have any variable. 
The significant thing here is that this is the **whole derivative** of I, not a partial derivative. So this means we can find the global minimum point of our cost, and we get to find out which $\epsilon$ gives us that minimum cost path. We need to **set the derivative of I to be 0, near the $\epsilon = 0$ point**. This is just calculus101, minimum/maximum where derivative is 0.

Then we move the derivative inside the integral:
$$\int_{x_1}^{x_2}\left.\frac{d}{d\epsilon}(F[x,\bar{y}(x),\bar{y}'(x)])\right|_{\epsilon=0}dx=0$$
> [!INFORMATION] Moving the derivative inside the integral - this is Leibniz rule.
> We are allowed to do this because it is a definite integral, with $x_1$ and $x_2$ fixed.

To take the derivative with respect to $d\epsilon$,  we can't just take the derivative of F with respect to $\epsilon$, because F is not a function of $\epsilon$, actually the sub functions, $\bar y$ and $\bar y'$ are functions of $\epsilon$: $$\int_{x_1}^{x_2}\left.(\frac{\partial F}{\partial x}.\frac{\partial x}{\partial\epsilon}+\frac{\partial F}{\partial \bar{y}}.\frac{\partial \bar{y}}{\partial\epsilon}+\frac{\partial F}{\partial \bar{y}'}.\frac{\partial \bar{y}'}{\partial\epsilon})\right|_{\epsilon=0}dx=0$$
This is the multivariable calculus - since F is not a function of $\epsilon$ we take the derivative of F wrt x, y, y' then chain it with derivatives of the respective vars wrt $\epsilon$. 
Since x is not a function of $\epsilon$, and only part of y and y' that are dependent on $\epsilon$ are $\eta$, (as evident on the partial derivatives with respect to $\epsilon$ from above): our equation collapses to the following:
$$\int_{x_1}^{x_2}\left.(\frac{\partial F}{\partial\bar{y}}.\eta+\frac{\partial F}{\partial\bar{y}'}\eta')\right|_{\epsilon=0}dx=0$$
> [!ERROR] We must evaluate this at $\epsilon=0$!! 
> So that this variation approaches the optimal solution. As $\epsilon$ approaches 0, we get closer to optimal path, y. and we have the optimal at exactly $\epsilon=0$. 

As $\epsilon=0$, we no longer need $\bar{y}$, just need y, as it is now the optimal. **So what the fuck was the point of $\bar{y}$ the whole time?**
Then we can get rid of bar from our integrals
$$\int_{x_1}^{x_2}\left.(\frac{\partial F}{\partial y}.\eta+\frac{\partial F}{\partial y'}\eta')\right|_{\epsilon=0}dx=0$$
This is called the 1st variation **Weak Form**, because we have $\eta'$, in it's derivate, not in it's pure form. So if we evaluate this integral now, we apply integration by parts:
$$\left.\frac{\partial F}{\partial y'}\eta\right|_{x_1}^{x_2}+\int_{x_1}^{x_2}(\frac{\partial F}{\partial y}-\frac{d}{dx}(\frac{\partial F}{\partial y'}))\eta dx=0 $$
Now we have the **strong form**. It is only in terms of $\eta$ now.
The first term cancels out to 0, as $\eta$, which is the deviation from the optimal path, is 0 at the waypoint themselves ($x_1$ and $x_2$). No matter how much we deviate from the optimal path, at least the start and finish must be the same. 

But $\eta$ is arbitrary, which means it can be anything. This forces the other part that it is multiplying, to be 0. This is the fundamental lemma of calculus of variations. So even the second term is equal to 0.
$$\frac{\partial F}{\partial y}-\frac{d}{dx}(\frac{\partial F}{\partial y'})=0$$ This is the [[Euler-Lagrange Equation]]!!!!!

# Example - littoral zone crossing
USV in deep open water, needs to reach a target waypoint located closer to the shore.
- In deep water, the vehicle can cruise at a fast speed $(v_{deep}=10ms^{-1})$
- In shallow water, drag increases dramatically, we slow to crawl $(v_{shallow}=4ms^{-1})$. 
The goal is to write a pathing algorithm that gets the vehicle to the waypoint in the absolute minimum time possible.

The standard way to solve this would be to just set up an equation of the cost based on a crossing coordinate where we cross the boundary, then take the derivative to find the minimum point. 

If this boundary between the shallow and deep water are swirling and not an easy shape to work with, we may not have a single x, we may have a whole list of points we adjust to find the optimal path.

Conventional calculus takes derivative with respect to a single coordinate, but Variational Calculus allows us to take the derivative of the total cost with respect to the **entire shape of the path**.
