Unlike existing trajectory planning solutions, MIGHTY performs joint spatial-temporal optimisation. The challenge with spatial-temporal decoupled approach is that increasing the number of decision variables such as control points, segement durations can improve performance, but comes at a cost of increased computational cost. 

MIGHTY employs Hermite spline representation and an unconstrained non-linear optimiser, searching directly over spatial waypoints, endpoint derivatives and segment durations. 

# Problem Formation
We formulate the **x** state vector as position and its derivatives up to *k*. So if one picks k = 2: $$x=\begin{bmatrix}pos \\ vel \\ acc \end{bmatrix}$$
And this also becomes the basis for spline knots. And we construct this big **H** matrix, which are organised as the following:
$$H_{(s,k)}= \begin{bmatrix}pos_0 & vel_0 & acc_0 & pos_1 & vel_1 & acc_1\\ pos_1 & vel_1 & acc_1 &pos_2 & vel_2 & acc_2 \\ && \vdots \\ pos_{s-1} & vel_{s-1} & acc_{s-1} & pos_s & vel_s & acc_s\end{bmatrix} = \begin{bmatrix}segment_0\\ segment_1 \\ \vdots \\ segment_s\end{bmatrix}$$
Because the state vector at any point, has to be between two knots, They must be expressed as a polynomial which satisifes all the conditions at the two knots:
1. Position at A
2. Velocity at A
3. Acceleration at A
4. Position at B
5. Velocity at B
6. Acceleration at B
To satisfy all 6 constraints, we need at least 5th degree polynomial.
$$x(t)=c_0+c_1t+c_2t^2+c_3t^3+c_4t^4+c_5t^5$$
To get the state vector in the middle of a segment:
$$x_s(\tau_s)=\sum_{k=0}^{5}h_k(\tau_s)H_{s,k}$$
Here $h_k(\tau_s)$ denotes the standard quintic Hermite basis functions, which allows us to seamlessly phase the starting segment (pos0, vel0, acc0) and the ending segment (pos1, vel1, acc1). They are formulated such that at $\tau=0$, pos0 has 100% influence. Then we observe, at $\tau=0$ and $\tau=T_s$, the velocities and accelerations again have 0% influence, and pos1 has 100% influence. This isolation allows us to have the velocities and acceleration that are decoupled from position. So changing the segments positions are mathematically decoupled from velocity and acceleration. Even if you change the position of a segment, it will not change the **starting and ending velocity and acceleration**.
## Quintic Hermite Basis Functions (k=2)
**Start knot (phase-out):**
$$ \begin{aligned} h_0(\tau) &= 1 - 10\tau^3 + 15\tau^4 - 6\tau^5 && \text{(position)} \\ h_1(\tau) &= \tau - 6\tau^3 + 8\tau^4 - 3\tau^5 && \text{(velocity)} \\ h_2(\tau) &= \frac{1}{2}\tau^2 - \frac{3}{2}\tau^3 + \frac{3}{2}\tau^4 - \frac{1}{2}\tau^5 && \text{(acceleration)} \end{aligned} $$ **End knot (phase-in):** $$ \begin{aligned} h_3(\tau) &= 10\tau^3 - 15\tau^4 + 6\tau^5 && \text{(position)} \\ h_4(\tau) &= -4\tau^3 + 7\tau^4 - 3\tau^5 && \text{(velocity)} \\ h_5(\tau) &= \frac{1}{2}\tau^3 - \tau^4 + \frac{1}{2}\tau^5 && \text{(acceleration)} \end{aligned} $$
## Decision Variables
We optimise the interior positions, velocities, accelerations and per-segment durations:
$$z=\begin{bmatrix}p_1^⊤&v_1^⊤&a_1^⊤& \cdots&p_{M-1}^⊤&v_{M-1}^⊤&a_{M-1}^⊤&T_0&T_1&\cdots&T_{M-1}\end{bmatrix}^⊤$$
The key here is that spatial states (p, v, a) and temporal state (T) are optimised jointly. This allows the optimiser to be able to modify the trajectory and also tune the time T, to speed up and down.
Additionally, optimising the raw derivative knots (v,a) can be numerically unstable. We can apply scalings to improve stability. 
## Representation
Evaluating costs and gradients directly in Hermite spline representation is expensive. It has a bunch of high order polynomials, like $\tau^5$ terms, which can be numerically unstable and expensive. Instead, we can optimise in the Hermite parameterisation, but evaluate the costs in the Bezier basis. For more details, refer to [[Bezier Curve]]

## Objective and Closed-Form Gradient
Expressing constraints in Hermite spline space, spatial-temporal space is difficult. This is because Hermite spline is not a convex hull. This makes it really difficult to judge whether a constraint is violated. It essentially needs to check every single point along the spline numerically. 
So at every loop of optimisation, we must take the optimiser output in Hermite spline space, convert it to Bezier space then evaluate against our constraints, then feed it back to the optimiser. As long as we are operating in Bezier curve space, we have the convex hull property, and also derivatives of a Bezier curve is another Bezier curve with 1 lower degree of polynomial. 

The conversion between Bezier control point $c$ and Hermite state $p, v, a$ are as follows:
$$\begin{gather*}c_{s,0}=p_s \\
c_{s,1}=p_s+\frac{T_s}{5}v_s \\
c_{s,2}=p_s+\frac{2T_s}{5}v_s+\frac{T_s^2}{20}a_s \\
c_{s,3}=p_{s+1}-\frac{2T_s}{5}v_{s+1}+\frac{T_s^2}{20}a_{s+1} \\
c_{s,4}=p_{s+1}-\frac{T_s}{5}v_{s+1} \\
c_{s,5}=p_{s+1}
\end{gather*}$$
and we define $c_s=\begin{bmatrix}c_{s,0}^⊤ & \cdots & c_{s,5}^⊤\end{bmatrix}^⊤$ and $y_s=\begin{bmatrix}p_{s}^⊤ & v_{s}^⊤ & a_{s}^⊤ & p_{s+1}^⊤ & v_{s+1}^⊤& a_{s+1}^⊤\end{bmatrix}^⊤$, and so $c_s=C(T_s)y_s$. $C(T_s)$ is a matrix which encodes the p,v,a of the knots to the control points of the Bezier curve:
$$C(T_s)=\begin{bmatrix}
1 & 0 & 0 & 0 & 0 & 0 \\
1 & \frac{T_s}{5} & 0 &0 &0 & 0 \\
1 & \frac{2T_s}{5} & \frac{T_s^2}{20} & 0 & 0 & 0\\ 
0 & 0 & 0 & 1 & -\frac{2T_s}{5} & \frac{T_s^2}{20} \\
0 & 0 & 0 & 1 & -\frac{T_s}{5} & 0 \\
0 & 0 & 0 & 1 & 0 & 0\end{bmatrix}$$
We also define $g_{c_s} \equiv \frac{\partial J_s}{\partial c_s}$, which is **how the cost function changes in [[Bezier curve]]**. This is the control knob we have, to encode where we should be discouraged to go. 

The goal here is to obtain the partial derivative of the cost function $J$ with respect to the decision variable $z$, which has position, its derivatives and the time information at the knots. Because **the cost function is expressed in [[Bezier curve]]** $c_s$, we cannot take the direct partial derivative with respect to z, instead we must compute *derivatives with respect to $y_s$* which bakes in the spatial information, and *then take the temporal derivative* with $T_s$, And *then apply the chain rule back to $z$ space*.

Then we can say:
$$\begin{aligned}\frac{\partial J_s}{\partial y_s} = \frac{\partial J_s}{\partial c_s} * \frac{\partial c_s}{\partial y_s}=C(T_s)^⊤g_{c_s} && \text{(1)}\end{aligned}$$
$$\begin{aligned}\frac{\partial J_s}{\partial T_s}=\left.\frac{\partial J_s}{\partial T_s}\right\rvert_{explicit}+\frac{\partial J_s}{\partial c_s}*\frac{\partial c_s}{\partial T_s}= \left.\frac{\partial J_s}{\partial T_s}\right\rvert_{explicit}+g_{c_s}^⊤\frac{\partial c_s}{\partial T_s} && \text{(2)}\end{aligned}$$

The explicit partial derivative part depends on the formation of the cost function.

Now both Eq. 1 and 2 can be expressed in the closed form. We just need to compute $g_{c_s}$ and $\left.\frac{\partial J_s}{\partial T_s}\right\vert_{explicit}$. 

The paper presents two types of cost terms: **control-points based** and **sampled states along the Hermite spline**.
## Control Points Based Cost
As an example, consider the integrated squared jerk expressed in terms of the coefficients/control points. Jerk is a derivative of acceleration, and because we are working on discrete time domain, we take the forward difference 3 times, to get jerk in terms of our control points.$$\Delta_{s,m}=c_{s,m+3}-3c_{s,m+2}+3c_{s,m+1}-c_{s,m}$$This is just what we get by taking the forward difference 3 times. e.g)
$\Delta^1c_m=c_{m+1}-c_m$  and $\Delta^2c_m=c_{m+2}-2c_{m+1}+c_m$ and the taking the third yields above.
Then the integrated squared jerk on segment $s$ is:
$$J_{smooth,s}=\int_0^{T_s}\|j(t)\|^2dt$$
To simplify this integral, we want to express this integral from 0 to 1, instead of the segment-variant Ts. We create a normalised clock $\tau_s$, which is defined as $\tau=\frac{t}{T_s}$, so that $\tau$ spans from 0 to 1, as t goes from 0 to $T_s$.
$$
J_{smooth,s}=C_s\int_0^1\|j(t)\|^2T_sd\tau_s
$$
Now our integration is in terms of the normalised clock, but our jerk vector $j(t)$ is still expressed in terms of raw time. We must perform chain rule to convert the expression in terms of the normalised clock.
Take velocity for example:
$$v(t)=\frac{d}{dt}p(\tau_s)=v(\tau_s)\times \frac{d\tau_s}{dt}$$
Here, as $\tau_s$ is just $\frac{t}{T_s}$, the derivative w.r.t t is simply $\frac{1}{T_s}$. 
Now our jerk vector is third derivative of position, so it can be expressed as:
$$j(t)=\frac{1}{T_s^3}j(\tau_s)$$
If we substitute our new jerk term in the normalised clock:
$$J_{smooth,s}=\int_0^1\|\frac{1}{T_s^3}j(\tau_s)\|^2(T_sd\tau_s)$$
$$J_{smooth,s}=\frac{1}{T_s^5}\int_0^1\|j(\tau_s)\|^2d\tau_s$$
$$J_{smooth,s}=\frac{1}{T_s^5}\int_0^1\|\sum_{m=0}^260\Delta_{s,m}B^2_m(\tau_s)\|^2d\tau_s$$
$$J_{smooth,s}=C_s\int_0^1\|\sum_{m=0}^2\Delta_{s,m}B^2_m(\tau_s)\|^2d\tau_s$$
where $C_s=3600T_s^{-5}$. The m variable here is just the counter. Now that we have converted to Bezier representation, we have 3 control points, because every time we take the derivative of a Bezier curve, we have Bezier curve of a n-1 number of control points. This is a natural result as the number of polynomial goes down, the necessary control points to describe the curvature also goes down. 

The problem here is, we cannot work with this ugly integral. We are supposed to evaluate this cost function rapidly, and numerical integration is the source of all evil in real time computing. Good news is, there is only one variable in there $B_m^2(\tau_s)$, that is dependent on $\tau_s$, and the Bernstein basis polynomials are just constants. 

So we define the [[Gram Matrix]], G as the following:
$$G=\begin{bmatrix}\int_0^1B^2_0(\tau_s)B_0^2(\tau_s)d\tau_s & \int_0^1B^2_0(\tau_s)B_1^2(\tau_s)d\tau_s & \int_0^1B^2_0(\tau_s)B_2^2(\tau_s)d\tau_s \\ \int_0^1B^2_1(\tau_s)B_0^2(\tau_s)d\tau_s & \int_0^1B^2_1(\tau_s)B_1^2(\tau_s)d\tau_s & \int_0^1B^2_1(\tau_s)B_2^2(\tau_s)d\tau_s \\ \int_0^1B^2_2(\tau_s)B_0^2(\tau_s)d\tau_s & \int_0^1B^2_2(\tau_s)B_1^2(\tau_s)d\tau_s & \int_0^1B^2_2(\tau_s)B_2^2(\tau_s)d\tau_s\end{bmatrix}$$Refer to [[Bezier Curve]] to see how this gnarly Bernstein basis polynomials are evaluated, but the key insight here is that they are all constants. We can officially remove the expensive integral, and using this Gram Matrix, vastly simplify the cost function representation.
And also $\Delta_s=[\Delta_{s,0}^⊤,\Delta_{s,1}^⊤,\Delta_{s,2}^⊤]^⊤$, so this encapsulates the step differences between Bezier control points. Then our cost function simplifies dramatically.
$$\begin{aligned}J_{smooth}=\sum_{s=0}^{M-1}C_s\Delta_s^⊤Q\Delta_s &&Q=G\otimes I_3\end{aligned}$$
Here $\otimes$ is the [[Kronecker Product]].
55
## Sampled States Based Cost