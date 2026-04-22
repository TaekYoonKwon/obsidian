Stablisability tries to answer the question: given the inputs that I have, can I make this system stable.
## Definition 
A system is stablisable if all of the unstable and lightly damped eigenvectors are controllable.
- **Strict Mathematical Definition:** A system is stabilisable if all of its strictly unstable or marginally stable modes (eigenvalues where $Re(λ)≥0$ in continuous time, or $∣λ∣≥1$ in discrete time) are controllable. If a mode is uncontrollable but already strictly stable, the system is still stabilisable because that mode will naturally decay on its own. 
- **Practical Engineering Definition:** In practice, a system is considered stabilisable if all unstable _and lightly damped_ eigenvectors are controllable. While a lightly damped mode might be mathematically stable, in a real-world system, we usually need actuator authority to actively damp it out to meet performance criteria.
## Popops-Belevitch-Hautus (PBH) Test
The PBH test provides a powerful algebraic method to determine the controllability of a Linear Time-Invariant (LTI) system described by the state-space equations $\dot x=Ax+Bu$ (continuous) or $x_{k+1}=Ax_k+Bu_k$​ (discrete).
The system described by the system matrix A and the input matrix B, is **controllable** iff rank of $$ rank[(A-\lambda I)|B]=n  $$where n is the number of dimensions. 
### When does this not hold true?
1. Rank of $(A-\lambda I) = n$ except for eigenvalues lambda. If we subtract the system matrix A by a $\lambda$ that is not an eigenvalue, we will have the full rank system already anyway. The adjacent operation | B cannot remove the rank, so we just need to test $\lambda$ for eigenvalues. 
2. B needs to have some component in each eigenvector direction. When we do $(A-\lambda I)$ we are essentially making the axis with eigenvalue inactive, rank deficient. We need to make sure B can control that dormant axis.
3. If B is a random column vector, $B= rand(n,1)$, (A, B) will be controllable with high probability. Basically, what are the chances that something that was chosen at random line up perfectly with just few of the eigenvectors, and completely orthogonal to some axis?
### How many control components I need at least, for a given system
#### Multiplicity in eigenvalue $\lambda$
If a system matrix A has repeated eigenvalues, we need more than 1 column of B, for the control matrix. Having repeated eigenvalues mean there is more than 1 deficient axis at certain times. So this multiplicity of eigenvalue indicates, in the worst case, how many axis may be "disabled" simultaneously and how many simultaneous inputs we may need to provide, which is the number of rows for control matrix B. 
Sometimes if two eigenvalues are close enough, they might be marginally close, and we may still need more than 1 column of B. 