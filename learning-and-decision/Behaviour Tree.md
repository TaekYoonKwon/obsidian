Behaviour Tree is another paradigm in task scheduling. Traditionally software was designed as FSM (Finite State Machine) or HFSM (Hierarchical Finite State Machine). In State Machine design, each behaviour is defined in a state, and the number of transitions to define explode as $N^2$ to the number of states. Also, to extend a state machine, you will have to know about all the incoming and outgoing transitions, making it highly rigid. 

Behaviour tree presents this flow of logic as a tree, **a graph of directed vertices and edges**. Each sub-tree is a self-contained unit with one entry point and three exit conditions. Because of this property, you can literally **pick and drop a tree at any point** and the overall behaviour still works. This design pattern allows us to **modularise and parallelise** the development of a complex system to set of behaviours and independently develop the logic flow for each behaviour. 

> [!INFORMATION] Behaviour Tree's Main Advantage
> Individual behaviors can easily be reused in the context of another higher-level behavior, without needing to specify how they relate to subsequent behaviors

# Execution Flow
The execution flow of a behaviour tree flows from **tick**, from root to downstream. The root receives a tick signal, and this tick signal is **propagated downstream**, according to each node downstream's type. Each node returns one of the three statuses:
- Success: Finished. Task complete
- Failure: Finished. Task Failed
- Running: Task still running. Requires another tick to check

> [!INFORMATION] Continuous Evaluation  
BT is continuously evaluated, with the tick signal generated at some frequency. Each tick may choose a different branch because the world changed.

# Four Core Control-flow Nodes
## Sequence Nodes
Often drawn as $\rightarrow$ or a box with arrow, it ticks its children from left to right. It returns failure on the first child that fails, and **returns success only if all the children succeeds**. This represent a **logical AND**.
## Fallback/Selector Nodes
Often drawn as ?, it ticks its children from left to right. It returns **success on the first child that succeeds**. The rest of the children will not be ticked. Using this property, we can embed *priority*. This node represents **logical OR**.
## Parallel Nodes
Often drawn as $⇒$, it ticks all of its children every **tick regardless of any children failure**. It returns success/failure based on a threshold policy, a rule which says **"Success if more than N children succeeds"**.
## Decorator Nodes
Often drawn as a rhombus $\diamond$, this is a single-child wrapper that **modifies the result**, and also **selectively ticks the child** according to some predefined rule. There are many types of decorators, such as inverter, retry, timeout etc. 
# Execution Node
Leaves are at the end of trees, the nodes that don't have any children. These leaves can be **actions** and **conditions**. 
1. **Action Node**
   When a tick is received, executes a command. Returns *Success* if the action complete, *Failure* otherwise.
2. **Condition Node**
   Checks a proposition, returns *Success* or *Failure*. Condition Node never returns a status of *Running*.
# Design a Behaviour Tree 
A behavior is often composed of a sequence of **sub-behaviors that are task independent**, meaning that while creating one sub-behavior the designer does not need
to know which sub-behavior will be performed next. Sub-behaviors can be designed
recursively, adding more detail.
## Principles
### 1. Improve Readability using Explicit Success Conditions
### 2. Improve Reactivity using Implicit Sequences
### 3. Handle Different Cases using a Decision Tree Structure
### 4. Improve Safety using Sequences
### 5. Create Deliberative BTs using Backchaining
### 6. Create Un-Reactive BTs using Memory Nodes
### 7. Choose the Proper Granularity of a BT