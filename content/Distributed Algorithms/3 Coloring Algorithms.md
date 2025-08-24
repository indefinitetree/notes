## <span style="color:#d3a939">Coloring Cycles Fast</span>

Let's look at a very fast distributed algorithm. We will have a simple setting - A graph which is a <span style="color:#69b5e9"><em>directed cycle</em></span>. By directed, its not about the communication pathway, the underlying communication is still both ways, except each node has an extra information. (like whose on the left etc.) Each node has exactly one <span style="color:#69b5e9"><em>successor</em></span> and one <span style="color:#69b5e9"><em>predecessor</em></span>. Let's say we need a proper $k$-coloring i.e., each node has to be labelled with a label varying from $0$ to $k-1$ and labels of any pair of neighbors are always different.

We'll assume that initially the nodes are already colored with some color. This is a valid assumption in a real life setting, because each computer could be mapped to a unique identifier from a large set of identifiers. Think of this like an IP address. Currently, the number of colors is as large as the maximum value of any computer's identifier, we would like to reduce the number of colors.

Let's say we already have a coloring with 256 numbers (labels from $0$ to $255$ ).

![[Pasted image 20250708004101.png]]

One algorithm would be to follow a simple strategy: In each step, a node is active if it is a local maximum. The active nodes will then pick a color which is free from the color that the neighbors already have.  This process is continued until all the nodes stop changing their colors. It can be shown that we can reduce any number of coloring to a 3-coloring since each node has at most two neighbors in the graph. 

Let's write this algorithm as a pseudocode, we should keep in mind that all the nodes in the network run the same algorithm. Let $c$ be the unique identifier of the the node. Pseudocode is given as follows.

---
```
while(true){
	Send message c to all neighbors
	Receive messages from all neighbors. Let M be the set of messages received
	If c != 1 && c != 2 && c != 3 && c > max(M){
		c = min({1 , 2 , 3} \ M) 
	}
}
```
---

Let's call $c = 1 ,2 , 3$  as stopping state. Once a computer reaches a stopping state, it never changes it state, meaning eventually all the computers would reach this state and the process will end at some finite time. We can rewrite the algorithm without `while(true)` by breaking out of the loop whenever the node reaches the stopping state.

## <span style="color:#d3a939">Faster coloring with Unique identifiers</span>

In worst case, the algorithm above is not particularly efficient. For example, if we had a chain/cycle with increasing node values, in each round at most two nodes reach the stopping state, which means that it takes $\Theta(n)$ rounds until all nodes have stopped. But we can do much faster. For $n = 256$ unique identifiers initially, in one round, we can reduce the number of colors to $16$.

We can represent the unique identifiers in terms of binary. For example, let's take a node $a$ with value 
$$
v_a = (123)_{10} = (01111011)_{2}
$$
whose successor node $a'$  (marked with a prime) has values 
$$
v_{a'} = (47)_{10} = (00101111)_2
$$
Consider the index of the lowest bit that differs i.e., 
$$
v_a[i] ~\neq ~v_{a'}[i]
$$
Now let the index be $i$ in binary and the value of $v_a[i]$ be $j$ , where $j$ is $0$ or $1$. Define the new color of node $a$ as concatenation of $i$ and $j$ . In other words, set 
$$
v_a = 2 \cdot i + j
$$
Note that $i$ is at most 3 digits and $j$ is a single bit, thus this new color's value is less than $16$. This process is done by all the nodes in a single round. 

Note that this always produces a <span style="color:#69b5e9"><em>proper coloring</em></span>. To see this, consider a pair of nodes $a$ and $b$ so that $b$ is the successor of $a$ . By definition, $v_{a'} = v_b$ , we need to show that $v_a \neq v_b$.
If the indices in which $a$ differs from $a'$ is same as $b$ differs from $b'$ , then $j$ cannot be same for both as it would imply that $a$ doesn't differ from $a'$ in that index which is contradiction. If the indices in which those two differ are different, then $v_a$  can never be equal to $v_b$ since one of them will be at least be greater than the other by one regardless of what $j$ is chosen for both the nodes.

The algorithm reduces $2^x$ colors to $2x$ colors in one round. If we iterate the algorithm, we can reduce the number of colors $n$ to $6$ in **$O(\mathrm{log}^* ~ x)$** rounds. The reduction works only till the maximum number of colors is of size 3-bits, and maximum color value possible is $6$ in that case since index of $i$ is at max $2$ which is $(10)_2$ . Once we have reduced it to $6$ colors, we can then reduce it to $3$ colors by the algorithm we discussed previously in $3$ rounds. 

We must be careful about the time complexity especially when we deal with such small functions, because for practical purposes, the constants will start mattering! ($\log ^* n \leq 6$  for all practical $n$). But here, as a classic theoretic person in the context, we'll skip it anyways.

## <span style="color:#d3a939">2-Coloring Algorithms:</span>

Consider a undirected path graph. It's clear that it has a 2-coloring. A straightforward algorithm would be to perform $O(n)$ round computation where each node gets to know about the whole graph, then compute the coloring locally and then outputting the color, or maybe just find the distance from the start node and color based on the parity (in case of directed). 
### <span style="color:#8bd952">Canonical LOCAL Algorithm:</span>

LOCAL is a type of model used in distributed algorithm. It'll be defined properly in the upcoming sections, but for now its just as equivalent of what we have assumed till now - Each node has a unique ID and information about its neighbors and tries to output/compute some structure or property of the network.

In any $t$-round algorithm, any node in the network can gain information/topology of the network within a radius of $t$ distance. For a particular node, any such distributed algorithm can be viewed as a centralized algorithm being locally operated on a $t$-hop subgraph of that node (or $\text{ball}_G(v , t)$). At first round, we simulate it for $t$ radius, then we simulate it for $t-1$ radius and so on.

Thus, any $t$-round LOCAL algorithm can be mathematically viewed as a $f(\text{ball}_G(v , t))$ , where $f$ is some function that operates on the graph. The main essence is that the algorithm does not care about the topology of the graph that is greater than radius $t$ for any node.
### <span style="color:#8bd952">Lower bound for 2-coloring:</span>

Indeed, the straightforward algorithm we discussed is asymptotically as good as any other algorithm could do in terms of number of rounds. Let's prove this by contradiction. Suppose there is an algorithm $A$ which runs for $t = o(n)$ rounds and outputs a $2$-coloring for given a path graph. Let's cpnstruct a counter-example for which this algorithm $A$ would fail. 

Consider three graphs  $G_1 , G_2 , G_3$ , all having nodes with unique identifiers,  of path length $2t+1$ , with central node being $u , v , w$ . Let's run the algorithm on these three graphs and get the 2-coloring for each of these path graphs. Now, by pigeonhole principle, atleast two of $u , v , w$ should have the same color. WLOG, we assume $G_1 , G_3$ are such graphs having central node being colored with the same color. Note that the central nodes in all three graphs has exactly $t$ nodes in both the sides.

Given these info, construct a new graph $G$ , by adding a edge from one terminal node of $G_1$ to one terminal node of $G_3$ .

```
Color configuration of G1
c' - ... - c(u) - ... - c'

Color configuration of G3
c'' - ... - c(w)- ... - c''

Color configuration of G
c' - ... - c(u) - ... - c' - c'' - ... - c(w) - ... - c'' 
```

We have two cases:
1. If $c' \neq c''$ - The algorithm $A$ is wrong in this case for one of the graphs $G_1$ or $G_3$ as $c(u) = c(w)$ fixes the color configuration for every node and it should be same for both the graphs.
2. If $c' = c''$ - The algorithm $A$ is wrong in this case for graph $G$ (Assuming that $2t+1+2t+1 \leq n$ , as we have a monochromatic edge.

Thus, such an algorithm $A$ cannot exist with $4t+2 \leq n \implies t \leq \dfrac{n-2}{4}$ rounds. Note that for $t$ greater than this bound, this argument of "the algorithm working on $G$ is exactly equivalent to the algorithm independently working on $G_1$ and $G_3$ , then their results being combined directly", would fail, since now the radius of the central nodes will start overlapping with each other.

Thus, for any deterministic $2$-coloring algorithm, $\Omega(n)$ is the lower bound.

## <span style="color:#d3a939">Coloring on general graphs and trees:</span>

We already saw an $O(\log^* n)$-round algorithm for directed paths.  Let's now look at the lower-bound for $3$-coloring directed paths.
### <span style="color:#8bd952">Lower bound for 3-coloring directed paths:</span>

First of all, we define something called a $k$-ary , $c$-coloring function $f$ which takes $k$ inputs and produces an output value (a color here) from $\{1 , 2 , ... , c\}$ i.e., 
$$
f(a_1 , a_2 , ... , a_k) = c' \in \{1 , 2 , ... , c\} 
$$
with $a_1 < a_2 < a_3 < ... < a_k$  (for convenience) , as well as it satisfies the following condition:
$$
f(a_1 , a_2 , ... , a_k) \neq f(a_2 , a_3 , ... , a_k , a_{k+1})
$$

Let $A$ be a $t$-round $3$-coloring algorithm and $f_A(a_1 , a_2 , ... , a_{2t+1})$ be the color node $a_{t+1}$ (outputs at the end of $A$ when run on a directed path graph $a_1 \rightarrow a_2 \rightarrow ... a_{2t} \rightarrow a_{2t+1}$ . Now, if $A$ is a valid $3$-coloring algorithm, then its straightforward to see that $f_A$ is a $2t+1$-ary , $3$-coloring function, since it satisfies the condition mentioned above
$$
f_A(a_1 , a_2 , ... , a_{2t+1}) = c(a_{t+1}) \neq c(a_{t+2}) = f_A(a_2 , a_3 , ... , a_{2t+2})
$$
#### <span style="color:#78bff2">Claim:</span>
Let $f_{2t+1}$ be a $2t+1$-ary $c$-coloring function which outputs the color of node $a_{t+1}$.  Define $f_{2t}$ as
$$
f_{2t}(a_1 , a_2 , ... , a_{2t}) = \{f_{2t+1}(a_1 , a_2 , ... , a_{2t+1})~ | ~ a_{2t+1} > a_{2t}\}
$$
$f_{2t}$ is a $2t$-ary $2^c$ coloring function.
#### <span style="color:#78bff2">Proof:</span>
Before the proof, we first clarify some things here and there. First, this claim is <span style="color:#69b5e9"><em>purely mathematical</em></span>, meaning that the function isn't really doing anything but this is for a general function. Second, $f_{2t}$ has its output as a set, which is a subset of $\{1 , 2 , ... , c \}$ i.e., range of $f_{2t}$ is the <span style="color:#69b5e9"><em>power set</em></span> of $\{1 , 2 , ... , c\}$. This is by definition of function, since $f_{2t+1}$ has its outputs from $\{1 , 2 , ... , c\}$ . Thus, $f_{2t}$ by definition, is a $2^c$ coloring function. Now to prove the claim, we need to show that 
$$
f_{2t}(a_1 , a_2 , ... , a_{2t}) \neq f_{2t}(a_2 , a_3 , ... , a_{2t+1})
$$
We prove this by contradiction. Assume
$$
f_{2t}(a_1 , a_2 , ... , a_{2t}) = f_{2t}(a_2 , a_3 , ... , a_{2t+1})
$$
Now consider the element $f_{2t+1}(a_1 , a_2 , ... , a_{2t+1})$ present in the set $f_{2t}(a_1 , a_2 , ... , a_{2t})$. Since by assumption, the sets are equal, therefore this element must be present in the other set as well, i.e., there exists $a_{2t+2} > a_{2t+1}$ such that $f_{2t+1}(a_2, a_3, ... , a_{2t+2}) = f_{2t+1}(a_1 , a_2 , ... , a_{2t+1})$ . 

But this is a contradiction, since $f_{2t+1}$ is a $2t+1$-ary $c$-coloring function (by definition $a_{2t+2}$ shouldn't exist). Therefore, the sets are not equal. Note that in the case where $a_{2t+1}$ is the maximum possible element (in case of finite sets), $f_{2t}(a_2 , a_3 , ... , a_{2t+1})$ is an <span style="color:#69b5e9"><em>empty set</em></span> by definition.

Now we complete the proof for the lower bound. If we have a $t$-round algorithm $A$,  which gives a $3$-coloring on directed graphs, it can be viewed as $f_A$ , which is a $(2t+1)$-ary $3$-coloring function. Using the claim we made above, there exists a function $f'$ which is a $(2t)$-ary $2^3$-coloring function. which could be constructed as given in the claim.

Repeatedly using the claim again and again on the function $i$ times, we get a function $f^*$ which is a $(2t-i+1)$-ary $2^{2^{.^{.^{3}}}}$ -coloring function.

Let $i = 2t$ , then $f^*$ becomes a unary $\lambda$ -coloring function, where $\log^* \lambda = 2t+1$.  Now, for 
$$
t < \dfrac{1}{2} \log^* n -1 \implies \log^* \lambda <\log^* n \implies \lambda < n
$$
But since $f^*$ is a unary $\lambda$-coloring function, therefore $\forall ~ a_1 < a_2$  ,
$$
f^*(a_1) \neq f^*(a_2)
$$
which means that all $a_1 , a_2 , a_3 , ... , a_n$ will have different output values when they become inputs for $f^*$. But this is not possible, since $f^*$ is a $\lambda$-coloring function, with $\lambda < n$. This means that $f^*$ doesn't exist. 

Back tracing all the functions, $f'$ doesn't exist implies $f_A$ doesn't exist which implies that such an algorithm $A$ cannot exist. Hence, the lower bound on $3$-coloring directed paths.

This kind of proof technique where we define a function and then progressively kind of simulate rounds is also called <span style="color:#69b5e9"><em>Round-Elimination Technique</em></span>. In a general way, this technique can be viewed as reducing a $t$-round algorithm solving a problem $\Pi$ to a $t-1$ round algorithm solving a problem $\Pi'$ that could be then used to solve the original problem. In this case, the problem description stayed same, but in some problems, the problem description along with other properties might blow up exponentially. There is a tool called Round Eliminator which automatically does this round-elimination.

Also, note that this lower bound proof uses <span style="color:#69b5e9"><em>stronger and general claims</em></span> which implies that locality in some sense is a strong property regardless of the graph's whole topology.

---
### <span style="color:#8bd952">3-coloring on rooted trees:</span>

A similar algorithm can be applied for rooted trees as well. Follow the same strategy, but instead each node comparing its color with its <span style="color:#69b5e9"><em>parent</em></span> instead of its <span style="color:#69b5e9"><em>successor</em></span>. By this way, we can maintain the legality each round. We can view this algorithm on rooted trees working on each disjoint path on the tree separately from a node.

Once the color reduce is done, we can convert the $6$-coloring to $3$-coloring by the <span style="color:#69b5e9"><em>shift-down</em></span> technique. Each node adopts the color of its parent. The root chooses any color different from its current. This ensures legality and additionally, for each node, now its neighbors will be using at most two colors. 

Using <span style="color:#69b5e9"><em>shift-down</em></span> we can eliminate the colors $4 , 5 , 6$ with each color being eliminated in two rounds. Let's say we need to eliminate color $4$. Once the <span style="color:#69b5e9"><em>shift-down</em></span> is done, the nodes with color $4$ can freely choose any one color from $1 , 2 , 3$ since its neighbors use at most two colors. We can repeat this for colors $5$ and $6$ as well to get the $3$-coloring.
### $\Delta + 1$ <span style="color:#8bd952">coloring on bounded degree graphs:</span>

Lets now see a coloring algorithm for a general bounded degree graph along the lines of the algorithm we saw previously.

Consider a vertex $v$, and let it have $k$ (at most $\Delta$) neighbors: $u_1 , u_2 , ... , u_k$ . Initially each node takes it own ID as its color. Let $c(v)$ be the bit representation. Let $b(u_j)$ be the bit representation of the index of the first (least significant) bit where $c(v)$ differs from $c(u_j)$ for all $1 \leq j \leq k$ . Then $v$ sets it color to be 
$$
\text{concat}(b(u_1) ~, ~c(u_1)[b(u_1)] ~, ~ b(u_2) ~, ~c(u_2)[b(u_2)] ~ , ~ ... , ~ b(u_k) ~ , ~c(u_k)[b(u_k)] )
$$
where $c(u_i)[b(u_i)]$  represents the $b(u_i)$th bit in $c(u_i)$. 

This again can be viewed similar to the algorithm presented for rooted trees, here we have $\Delta$ parents at most for each node and we break the <span style="color:#69b5e9"><em>symmetry</em></span> with each parent. This algorithm reduces the number of bits in the colors from $\ell$ to at most $\Delta(\log \ell + 1)$ in one step. Thus, by applying this reduction $O(\log^* n)$ times, the number of bits in the colors reduce to at most $6\Delta$ . By doing a shift-down, it can be reduced to $3\Delta$. Hence the total number of colors in the graph is at most $2^{3\Delta}$ .

Now we reduce this to $\Delta + 1$ in $O(2^{3 \Delta})$ rounds. In each round, one color higher than $\Delta + 1$ is eliminated. This is straightforward - choose a set of nodes with color $c > \Delta + 1$ , let's say $\Delta + 2$ . Now find the minimum color that is not present among the neighbors of this node and recolor it with that color. Since initially legality was preserved before these rounds, no two adjacent nodes will be recoloring themselves, thus preserving legality. We continue the same process for nodes with color $\Delta + 3$ , and so on.

#### <span style="color:#78bff2">Claim:</span>

Any graph with maximum degree $\Delta$ can be colored in $O(2^{3\Delta} + \log^* n)$  rounds using $\Delta + 1$ colors. If we assume $\Delta = O(1)$ , then such a graph can be colored in $O(\log^* n)$ rounds.

#### <span style="color:#78bff2">Open Problems and Ideas:</span>

For $\Delta + 1$ coloring on general graphs, Linial gave an algorithm which runs in $O(\Delta^2 + \log^* n)$ rounds. Then the time complexity was further reduced to $O(\Delta + \log^* n)$ and for some specialized problems, to $O(\sqrt{\Delta} + \log^* n)$ . But all these algorithms are non-constructive. Can we make it constructive? How does the trade-off between $\Delta$ and $n$ on different algorithms?

Open question (Considered to be hard): Is $O(\Delta + \log^* n)$ round algorithm optimal for $\Delta + 1$ - coloring on general bounded degree graphs? Note that this lower bound has been proved for MIS.

## <span style="color:#d3a939">Exercises </span>

1. Is there a $2$-coloring algorithm which runs in less than $n$ rounds on a path graph of length $n$?
2. Design a $O(\log^* n)$ round algorithm that works on any path (not given a consistent orientation of the edges). 

## <span style="color:#d3a939">Resources:</span>
- https://github.com/olidennis/round-eliminator - Round Eliminator Tool that does automatic Round-Elimination. It also gives heuristic based reduction problems in case the problem description is too large.
- [Linial's Lower bound made easy](https://arxiv.org/pdf/1402.2552) by Juhana Laurinharju and Jukka Suomela.
- CS6851 Distributed Algorithms July-Nov 2025 Offering by Prof. Shreyas Pai.
- [Distributed Algorithms 2020](https://jukkasuomela.fi/da2020/) by Prof. Juho Hirvonen and Prof. Jukka Suomela (Chapter 1).