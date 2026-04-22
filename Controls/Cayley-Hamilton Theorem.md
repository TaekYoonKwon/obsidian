## Definition
The theorem states that if you take that scalar polynomial and replace the variable $\lambda$ with the matrix A itself (and replace the constant $a_0$ with $a_0 I$), the whole thing equals the zero matrix.
## Derivation
Every square matrix A satisfies its own characteristic equation. 
$$det(A-\lambda I) = 0$$
1. The characteristic equation goes as below. This is the standard polynomial you get when you calculate $det(A−\lambda I)=0$ to find the eigenvalues ($\lambda$).
$$\lambda^n + a_{n-1}\lambda ^{n-1} + \cdots a_2\lambda^2+a_1\lambda+a_0=0$$
2. Apply Cayley-Hamilton Theorem:
$$A^n+a_{n-1}A^{n-1}+\cdots+a_2A^2+a_1A+a_0I=0$$
3. Isolation of the highest power. By moving the highest power to the LHS, it turns out you never actually need to calculate the highest power term. It is just a linear combination of lower powers.
$$A^n=-a_0I-a_1A-a_2A^2+\cdots+a_{n-1}A^{n-1}$$
Finally, this means for every power greater than n,
$$A^{\geq n}=\sum_{j=0}^{n-1}\alpha_jA^j$$
The ultimate takeaway from this is that **no matter how high you raise the power of an n×n matrix, it will never generate a new, mathematically independent "direction."** Any power of A, up to $A^{1000000}$, is trapped inside a finite boundary. It can always be rewritten as a simple weighted sum (using some scalar coefficients $\alpha_j$​) of the base matrices: $I,A,A_2,…,A_{n−1}$.

## Significance
Take $$\dot{x} = Ax+Bu$$where $x\in \mathbb{R}^n$.
This system can be expressed as the convolution integral: $$x(t)=\int_{0}^{t}e^{A(t-\tau)}Bu(\tau)d\tau$$
"To find out where you are at time t, add up all the control inputs $u(\tau)$ you've applied, multiplied by how the system dynamics $e^{A(t−\tau)}$ carry those inputs forward in time."
$$e^{At}=I+At+\frac{A^2t^2}{2}+\cdots$$
Because of the Cayley-Hamilton theorem, we know we don't actually need infinite powers of A. Every power of A from n and above can be crushed down into a combination of lower powers.
Therefore, that infinite Taylor series perfectly collapses into a finite polynomial with some time-varying scalar coefficients ($\alpha$):
$$x(t)=\int_{0}^{t}(\alpha_0(t-\tau)I+\alpha_1(t-\tau)A+\alpha_2(t-\tau)A^2+\cdots+\alpha_{n-1}(t-\tau)A^{n-1})Bu(\tau)d\tau$$
We can pull A and B matrix, and the constants out. Then it becomes$$x(t)=\alpha_0B+\alpha_1AB+\alpha_2A^2B+\cdots+\alpha_{n-1}A^{n-1}B$$
Recall Controllability Matrix:
$$\xi = [B\space\space AB \space\space A^2B \space\cdots\space A^{n-1}B]$$

- **The Boundary of the Possible:** If a direction in your state space is not spanned by the columns of the controllability matrix $\xi$, you can **never** reach it. It doesn't matter how hard you push the actuators (u) or how long you wait (t); the math physically prevents the system from moving into that dimension.
    
- **Why we stop at n−1:** One might think, _"If I can't reach my target, maybe I just need to wait longer so the higher-order dynamics (like $A^5B$ or $A^{12}B$) can kick in!"_ This derivation proves that waiting longer doesn't unlock new directions. Because of Cayley-Hamilton, any higher-order movement is just a recycled path of the first n−1 movements. If you can't get there using the finite matrix $\xi$, you are permanently locked out.