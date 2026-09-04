Assuming a car-like vehicle, with underactuated configuration in the lateral direction. Dubin's curve tries to answer the question: knowing the start and end configuration $q=[x,y,\theta]$ of a given path, what is the shortest path that connects the two configurations? As the minimum turning radius of the system is defined, this is a bounded-curvature shortest path problem.

Let's first start with a trivial case, where the minimum turning radius is 0, so the vehicle can do a point turn. In this case, the shortest path will follow a straight line. The criterion we must optimise is:$$L(\tilde{q},\tilde{u})=\int_0^{t_F}\sqrt{\dot{x}(t)^2+\dot{y}(t)^2}dt$$where $t_F$ is the time at which the goal configuration is reached. This equation basically reads - the cost in terms of the state trajectory ($\tilde{q}$) and the control trajectory ($\tilde{u}$) is an integral of the speed from start to finish. 

In constructing the Dubin's curve and its geometry, we assume constant speed. This does not mean to say we must move in constant speed along the curve - this is just an assumption that the geometry makes. So we set our speed to 1, then the system simplifies to $$\begin{gather*}\dot{x}=cos(\theta)\\\dot{y}=sin(\theta)\\\dot{\theta}=u\end{gather*}$$where u in chosen from the interval $U=[-\tan(\phi_{max}), \tan(\phi_{max})]$ . 
> Where this comes from: $\tan(\phi_{max})$  is that if we have a vehicle turning, the line perpendicular to the turned wheel and the line extending sideways from the real axle, creates a right angle triangle, meeting at Instantenous Center of Rotation (ICR).$$\tan(\phi)=\frac{L}{R}$$
> Then we can express angular velocity as: $$\begin{gather*}V=\dot\theta\times R\\\dot\theta=\frac{V}{R}\\\dot{\theta}=\frac{V}{L}\tan(\phi)\end{gather*}$$
> Then we assume L = 1, and V = 1. 

With this system definition, our cost function reduces to optimising the time $t_F$ to reach $q_G$, as $\sin(\theta)^2+\cos(\theta)^2 = 1$. 

# Primitives
So we establish primitives which are like building blocks. The establishment of Dubin's curve is that between two configurations, the shortest path can be expressed as no more than three Dubin's primitive. 
- Each motion primitive applies a constant action over an interval of time
- Only actions needed to traverse the shortest paths are $u\in \set{-1,0,1}$, so hard left or right or no steering
- **S** = no steering
- **L** = positive steering 
- **R** = negative steering
>[!important] Depending on your coordinate system, L could be physical left or right turn. For NED, because positive angle is clockwise, **L** is actually a right hand turn.

With these building blocks and the characteristic of Dubin's curve, which promises no more than three curves are needed for optimal path, we can basically define six candidates for an optimal path:$$\set{LRL,RLR,LSL,LSR,RSL,RSR}$$But also, the duration of each primitive must be specified. 
For L and R (the rotational components), their respective subscript denote the total amount of rotation which accumulates during the primitive.
For S, the subscript denotes the total distance travelled. We give each segment the following subscripts:
$$\set{L_{\alpha}R_{\beta}L_{\gamma},R_{\alpha}L_{\beta}R_{\gamma},L_{\alpha}S_{d}L_{\gamma},L_{\alpha}S_{d}R_{\gamma},R_{\alpha}S_{d}L_{\gamma},R_{\alpha}S_{d}R_{\gamma}}$$
where $\alpha,\gamma\in[0,2\pi]$, $\beta\in[\pi, 2\pi]$ and $d\geq 0$.  
> Basically, every term must start with a circular term. Then ended with a circular term too. Then the first, second and third circular components are given the subscript $\alpha, \beta, \gamma$ respectively, while a straight segment, which may be there as the second segment, has the subscript d.

# Construct an actual curve - Geometric
As an example, we will take a look at LSL path between $q_s=(x_s,y_s,\theta_s)$ and $q_G=(x_g,y_g,\theta_g)$. 
1. Find the centers:
   First calculate the centers of the start-left circle and the goal-left circle. For a radius R:
   $x_{CSL}=x_s-R\sin(\theta_s)$
   $y_{CSL}=y_s+R\cos(\theta_s)$
   $x_{GSL}=x_g-R\sin(\theta_g)$
   $y_{GSL}=y_g+R\cos(\theta_g)$
   >They are derived from the fact that if you have a yaw angle theta, to get the unit vector pointing at the yaw angle, we represent it as [cos, sin]. To visualise this easier, take yaw angle 0, pure north. Then the left turn is a vector [0, 1], [-sin, cos]and the right turn is a vector [0, -1], [sin, -cos].
2. Calculate the tangent:
   Depending on the specific **word**, we may need an **inner common tangent** or **outer common tangent**. 
   - If you have a repeated arc between a straight component, we want the outer common tangent.
   - For the outer common tangent, it is simply a euclidean distance between the two centres. $d = \sqrt{(x_{CGL}-x_{CSL})^2+(y_{CGL}-y_{CSL})^2}$
   - If you have different arcs, then we want the inner common tangent. 
   - In this case, because we have an LSL path, we want outer common tangent. 
> Think of it as, inner common tangent is you cross the tangent between the two centers of circle. If you have two opposing turns, CW and CCW, it's impossible to not cross it, whereas if you have two same direction turns, it doesn't make sense to cross the cross the tangent because that would mean you are doing extra work. 

3. Find the Arc Length:
   - For the first turn, it is simply the angle needed to rotate from the initial heading to the heading of the tangent line. 
   - For the final turn, it is the angle needed to rotate from the tangent line heading to the final goal heading.
# Construct an actual curve - Analytical
We define three operators that compose with a configuration and produce the new configuration after travelling along that segment:
$$\begin{gather*} L_v(x,y,\phi)=(x+R\sin(\phi+v)-\sin\phi, y-\cos(\phi+v)+\cos\phi, \phi+v)\\R_v(x,y,\phi)=(x-\sin(\phi-v)+\sin\phi, y+\cos(\phi-v)-\cos\phi,\phi-v)\\S_v(x,y,\phi)=(x+v\cos\phi,y+v\sin\phi,\phi)\end{gather*}$$
so LSL curve would just be:
$$L_q(S_p(L_t(0,0,\alpha)))=(d,0,\beta)$$
# Finding the best curve
Okay, so now we can generate a bunch of Dubin's curve. How do we find the best among 6? There is a paper which mathematically guarantees which one of the candidate is the best without having to calculate all 6. https://bpb-us-e2.wpmucdn.com/faculty.sites.uci.edu/dist/e/700/files/2014/04/Dubins_Set_Robotics_2001.pdf
The reality is, because Dubin's curve is purely geometric, calculating 6 Dubin's curve is a trivial task for a modern processor. It is generally not worth the complexity required to implement this. 
To compare the curve, we can simply use the sum of arc lengths and line segment length.  