# Definition
Bezier curve allows efficient evaluation of the trajectory and its derivatives at arbitrary sample points. A degree N Bezier segment is written as:
$$x(u)=\sum_{i=0}^{n}B_i^n(u)c_i,u\in[0,1]$$
Where the vectors $c_i$ are the Bezier control points, and the Bernstein basis polynomials are
$$B^n_i(u)=\binom{n}{i}u^i(1-u)^{n-i}=\frac{n!}{i!(n-i)!}u^i(1-u)^{n-i}$$
Together they essentially describe how each order of polynomial in terms of each magnet point c, pulls the trajectory.

## Trajectory Representation
Because Bezier curve defines how each control points "pull" on a curve, for an $n_{th}$ degree of polynomial of curve, we need n+1 control points. The curve is then represented as a sum of each control point's influence over the curve.
$$p(\tau_s)=\sum_{i=0}^{n}c_iB_i^{n}(\tau_s)$$
## Taking the derivative
Use the product rule on $u^i(1-u)^{n-i}$:
$$\frac{d}{d\tau}B^n_i(u)=\frac{n!}{i!(n-i)!}[iu^{i-1}(1-u)^{n-1}-(n-i)u^i(1-u)^{n-i-1}]$$
This can be simplified to:
$$\frac{d}{dt}B_i^u(\tau)=n[B_{i-1}^{n-1}(\tau)-B_i^{n-1}(\tau)]$$

Here we see that if we differentiate the Bezier representation of the trajectory in position, we can get the velocity counterpart, take more derivatives for acceleration and jerk etc.
$$v(\tau_s)=\frac{d}{dt}p(\tau_s)=n\sum_{i=0}^nc_iB_{i-1}^{n-1}(\tau)-n\sum_{i=0}^nc_iB_i^{n-1}(\tau)$$
We want to simplify this term, but because the Bernstein basis polynomial on the first and the second term are of different index, we cannot naively combine them. Luckily, because the $B_{-1}$ of the Bernstein basis polynomial is 0, we can do a shift on the first term like $$n\sum_{i=0}^nc_iB_{i-1}^{n-1}(\tau)=n\sum_{i=0}^{n-1}c_{i+1}B_{i}^{n-1}(\tau)$$ Then we can simplify: 
$$v(\tau_s)=n\sum_{i=0}^{n-1}c_{i+1}B_{i}^{n-1}(\tau) - n\sum_{i=0}^nc_iB_i^{n-1}(\tau)=n\sum[c_{i+1}-c_i]B_i^{n-1}(\tau)$$
And even more, $[c_{i+1}-c_i]$ is the definition of $\Delta^1$. 
$$
v(\tau_s)=n\sum_{i=0}^{n-1}\Delta^1c_iB_i^{n-1}$$
$$a(\tau_s)=\frac{d}{dt}v(\tau_s)=n\times (n-1)\sum_{i=0}^{n-2}\Delta^2c_iB_i^{n-2}(\tau_s)$$
$$j(\tau_s)=\frac{d}{dt}v(\tau_s)=n\times (n-1)\times(n-2)\sum_{i=0}^{n-3}\Delta^3c_iB_i^{n-3}(\tau_s)$$

This proves **Hodograph theorem**: when you take the calculus derivative of a Bezier curve, the result is simply another Bezier curve. 

The benefit of having this representation is so that $B_i^n(u)$ can be precalculated, and saved to a buffer, since it doesn't have any dependency on each control points or even time. Additionally, when we evaluate the optimised trajectory to feed it into the controller, the Hermite spline has velocity term and acceleration term as separate notation. But each velocity and acceleration term contains $\tau^n$ terms which can be extremely small and cause numerical instability. It is more numerically stable to convert the Hermite spline to Bezier curve in position trajectory, then perform differentiation on it to find the velocity.