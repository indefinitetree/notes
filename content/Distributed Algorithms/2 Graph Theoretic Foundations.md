---
title: 2 Graph Theoretic Foundations
---
--- 
Basic Terminologies such as
- Undirected and directed graphs
- Adjacency , Degree , k-regular 
- Subgraphs , Induced subgraph (Vertex/Edge induced) , Dpanning subgraph
- Walks, Cycles, Paths, Trail
- Connectivity, Distance, Diameter, Tree, Isomorphism

are assumed to be known already.

---
## <span style="color:#d3a939">Packing and Covering</span>

A subset of nodes $X \subseteq V$  is 
- an <span style="color:#69b5e9"><em>independent set</em></span> if each edge has at most one endpoint in $X$ . In other words, $| e \cap X| \leq 1$ for all $e \in E$ .
- a <span style="color:#69b5e9"><em>vertex cover</em></span> if each edge has at least one endpoint in $X$ . In other words, $e \cap X \neq \emptyset$ for all $e \in E$ .
- a <span style="color:#69b5e9"><em>dominating set</em></span> if each node $v \notin X$ has at least one neighbor in $X$. In other words, $\mathrm{ball}_G(v , 1) \cap X \neq \emptyset$ for all $v \in V$, where 
$$\mathrm{ball}_G(v,r) = \{ u \in V : \mathrm{dist}_G(u , v) \leq r \}$$ 
A subset of edges $X \subseteq E$ is
- a <span style="color:#69b5e9"><em>matching</em></span> if each node has at most one incident edge in $X$. In other words, $\{t , u\} \in X ~  \&  ~ \{t , v\} \in X ~ \implies u = v$ .
- an <span style="color:#69b5e9"><em>edge cover</em></span> if each node has at least one incident edge in $X$ . In other words, $\bigcup_{i \in {u ,v}} X = V$ .
- an <span style="color:#69b5e9"><em>edge dominating set</em></span> if each edge $e \notin X$ has at least one neighbor in $X$. In other words, $e \cap \left(\bigcup X\right) \neq \emptyset$ for all $e \in E$. 

## <span style="color:#d3a939">Labelings and Partitions</span>

We will often encounter functions of the form 
$$\displaystyle{f : V \rightarrow \{1 , 2 , ... , k\}} $$
There are two interpretations that are often helpful:
- Function $f$ assigns a label $f(v)$ to each node $v \in V$.
- Function $f$ is a partition of $V$. More specifically, $V = V_1 \cup V_2 ~ \cup ... ~\cup V_k$ where $V_i = f^{-1}(i) = \{v \in V : f(v) = i\}$ .

Similarly, we can have functions of the form 
$$ \displaystyle{f : E \rightarrow \{1 , 2 , ... ,k\}}$$
and interpret it as a labeling of edges or as a partition of $E$ .

We say that a function $f : V \rightarrow \{1, 2 ,.. , k\}$ is
- a <span style="color:#69b5e9"><em>proper vertex coloring</em></span> if $f^{-1}(i)$ is an independent set for each $i$ .
- a <span style="color:#69b5e9"><em>weak coloring</em></span> if each non-isolated node $u$ has a neighbor $v$ with $f(u) = f(v)$ .
- a <span style="color:#69b5e9"><em>domatic partition</em></span> if $f^{-1}(i)$ is a dominating set for each $i$.

A function $f: E \rightarrow \{1 , 2 , ... , k\}$ is
- a <span style="color:#69b5e9"><em>proper edge coloring</em></span> if $f^{-1}(i)$ is a matching for each $i$ 
- an <span style="color:#69b5e9"><em>edge domatic partition</em></span> if $f^{-1}(i)$ is an edge dominating set for each $i$.

 ![[Pasted image 20250708163735.png]] 
## <span style="color:#d3a939">Factors and Factorizations</span>

Let $G = (V , E)$ be a graph, let $X \subseteq E$ be a set of edges, and let $H = (U , X)$ be the subgraph of $G$ induced by $X$ . We say that $X$ is a $d$-factor of $G$ if $U = V$ and $\mathrm{deg}_H(v) = d$ for each $v \in V$ .

Equivalently, $X$ is a $d$-factor if $X$ induces a spanning $d$-regular subgraph of $G$. A function $f: E \rightarrow \{1 , 2 , ... , k\}$ is a $d$-factorization of $G$ if $f^{-1}(i)$ is a $d$-factor for each $i$ .

Following observations can be made from this
- A $1$-factor is a <span style="color:#69b5e9"><em>maximum matching</em></span>. A $1$-factorization is an edge coloring.
- The subgraph induced by a $2$-factor consists of <span style="color:#69b5e9"><em>disjoint cycles</em></span>.
- A $1$-factor is also known as <span style="color:#69b5e9"><em>perfect matching</em></span>.

## <span style="color:#d3a939">Approximations</span>

Formally, the definition of a <span style="color:#69b5e9"><em>maximization</em></span> problem consists of two parts: a set of feasible solutions $F$ and an objective function $g: F \rightarrow \mathbb{R}$ . In a <span style="color:#69b5e9"><em>maximization</em></span> problem, the goal is to find a feasible solution $X \in F$ that maximizes $g(X)$. A <span style="color:#69b5e9"><em>minimization</em></span> problem is analogous.

Often it is infeasible or impossible to find an optimal solution, hence we resort to approximations. Given a maximization problem $(F , g)$, we say that a solution $X$ is an $\alpha$-approximation if $X \in F$, and we have $\alpha \cdot g(X) \geq g(Y)$ for all $Y \in F$ . Note that $\alpha \geq 1$ in this case. The same applies for a minimization problem as well, with the constant on the other side with reverted inequality.

---

Let's prove a example theorem.
###### <span style="color:#ba7bee">Claim:</span> 
In any $d$-regular graph $(d \geq 1)$, a <span style="color:#69b5e9"><em>minimum vertex cover</em></span> is always a $d$-approximation of a <span style="color:#69b5e9"><em>minimum dominating set</em></span>.

![[Pasted image 20250708170452.png]]

###### <span style="color:#ba7bee">Proof:</span>
First let us a show that a <span style="color:#69b5e9"><em>minimum vertex cover</em></span> is a <span style="color:#69b5e9"><em>dominating set</em></span>. Let $X$ be any vertex cover. It is fairly straightforward that since $X$ is a vertex cover, every edge has at least one endpoint in $X$, which means any node $u$ which is not in $X$ has at least one of its neighbor in $X$.

Now to show that the <span style="color:#69b5e9"><em>minimum vertex cover</em></span> is always a $d$-approximation of a <span style="color:#69b5e9"><em>minimum dominating set</em></span>, consider a <span style="color:#69b5e9"><em>minimum vertex cover</em></span> $X'$ and a <span style="color:#69b5e9"><em>dominating set</em></span> $Y$. We need to show that $|X'| \leq d \cdot |Y|$ . 

Since the graph is $d$-regular, therefore $(d+1) \cdot |Y| \geq |V|$ . This is because for any vertex in $Y$ , since it has a degree $d$ and by the definition of <span style="color:#69b5e9"><em>dominating set</em></span>, the <span style="color:#69b5e9"><em>union</em></span> of neighbors ($d$ neighbors) and vertex itself from all the vertices in $Y$ must contain all the vertices in the graph.

Consider $A = V \setminus X'$ , the set of nodes that are not in the <span style="color:#69b5e9"><em>minimum vertex cover</em></span> $X'$. 

![[Pasted image 20250708171800.png]]  

Similar to the above argument, $(d+1)\cdot |A| \geq |V|$ . Again to put in perspective, this says that the <span style="color:#69b5e9"><em>union</em></span> of all neighbors and vertex itself for each vertex in $A$ equals $V$. This is not really evident at first. Let's prove this by contradiction. Suppose there was a vertex $u$ such that it is missed out while taking the <span style="color:#69b5e9"><em>union</em></span>. Now $u \notin A$ and all the neighbors of $u$ are also not in $A$. This means that $u$ and all the neighbors of $u$ both belong to the set $X'$ . But this leads to a contradiction, since removing $u$ from this set still gives us a <span style="color:#69b5e9"><em>vertex cover</em></span> which has a cardinality lesser than $X'$. 

The result now follows up eventually. We know 
$$ \displaystyle{|A| \geq \dfrac{|V|}{d+1}} $$

Since $|X'| = |V| - |A|$ , therefore 
$$\displaystyle{|X'| \leq \dfrac{d}{d+1} |V|}$$
Previously, we had $|V| \leq (d+1) \cdot |Y|$ . Combining these two, we get
$$\displaystyle{|X'| \leq d \cdot |Y|}$$
which is the required result.

One key point to note is that we didn't really use the property that $X'$ is a <span style="color:#69b5e9"><em>minimum vertex cover</em></span>, rather we used the property that any minimum vertex cover is also a <span style="color:#69b5e9"><em>minimal vertex cover</em></span>. Thus, this claim holds true even when we consider any <span style="color:#69b5e9"><em>minimal vertex cover</em></span>.

What if we used any <span style="color:#69b5e9"><em>vertex cover</em></span> instead of $X'$ ? The statement doesn't hold true now. The simplest counterexample would be considering the vertex cover that covers all the vertices. Choose $|V| = 4$ and $d = 3$. Here $|Y| = 1$ , while $|X| = 4$, contradicting the claim.

---
## <span style="color:#d3a939">Exercises</span> 

Will be added soon