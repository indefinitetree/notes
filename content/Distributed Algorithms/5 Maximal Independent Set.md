---
title: 4 Local Symmetry Breaking
date: 2025-07-11
---
---
We will look at problems which are <span style="color:#69b5e9"><em>local</em></span> symmetry breaking, where the goal is to break <span style="color:#69b5e9"><em>symmetry</em></span> among nodes that are quite close, typically neighbors. A fundamental problem in this category is the <span style="color:#69b5e9"><em>Maximal Independent Set (MIS)</em></span> problem. We will look at a <span style="color:#69b5e9"><em>MIS</em></span> Algorithm that takes $O(\log n)$ rounds. Another way to view this analysis is that each node (with high probability) needs only information about its $O(\log n)$ neighborhood. 

A fundamental open question in distributed computing is to fully characterize the <span style="color:#69b5e9"><em>locality</em></span> of <span style="color:#69b5e9"><em>MIS</em></span>. We can show a highly non-trivial locality lower bound of $\Omega(\log ^* n)$ for MIS. 

Besides <span style="color:#69b5e9"><em>MIS</em></span>, another <span style="color:#69b5e9"><em>local symmetry breaking</em></span> problem is <span style="color:#69b5e9"><em>coloring</em></span>. Both are related to each other in some way, yet they have their own characteristics. 

We will consider the synchronous <span style="color:#69b5e9"><em>LOCAL</em></span> model, although all the algorithms will work seamlessly in the <span style="color:#69b5e9"><em>CONGEST</em></span> model as well, for the following problems. As usual we consider the network as an undirected connected graph $G = (V,E)$ of $n$ nodes. All nodes have unique identifiers and can be represented in $O(\log n)$ bits. We also assume that all nodes are awake initially and start executing the algorithm simultaneously.

---
## <span style="color:#d3a939">Maximal Independent Set (MIS)</span>

An useful property of <span style="color:#69b5e9"><em>MIS</em></span> is that it is also a <span style="color:#69b5e9"><em>dominating set</em></span>. In fact it is a <span style="color:#69b5e9"><em>minimal dominating set (MDS)</em></span> as well. A <span style="color:#69b5e9"><em>dominating set</em></span> could be used as a network backbone for routing - it is enough to find routes between the nodes in the <span style="color:#69b5e9"><em>dominating set</em></span>; any other node can route by sending it to any of its dominator first.

### <span style="color:#8bd952">Fast Distributed MIS Algorithms:</span>

The output of each node in this problem would be whether the corresponding node is in the <span style="color:#69b5e9"><em>MIS</em></span> or not. Let $\Gamma(v)$ be the set of vertices in $V$ that are adjacent to $v$. Let $N(v)$ denote the close neighborhood of $v$, i.e., the set consisting of $v$ and $\Gamma(v)$ .

The outline of the algorithm would be somewhat like this . In every round, it finds an <span style="color:#69b5e9"><em>independent set</em></span> $S$ and adds it to $I$ (which is empty initially) as well as deletes $S \cup \Gamma(S)$ from the graph. This is easy to implement in a linear fashion - Choose some arbitrary node, turn off its neighbors (essentially deleting the nodes) and continue the same process with the remaining graph, until the graph becomes empty.

To get a faster algorithm, an obvious strategy is to include as many nodes in the <span style="color:#69b5e9"><em>MIS</em></span> in a round as possible. Consider a node $v$. Clearly, either $v$ or at least one of its neighbors should be in the <span style="color:#69b5e9"><em>MIS</em></span>. 

#### <span style="color:#78bff2">MIS Algorithm 1:</span>

Each node chooses itself to be in the <span style="color:#69b5e9"><em>MIS</em></span> with probability $\dfrac{1}{2 \cdot d(v)}$ . To handle the scenario of two neighboring nodes choosing themselves, the ties are broken based on the degrees with higher degree node favored. This makes sense since once this high degree node comes to <span style="color:#69b5e9"><em>MIS</em></span>, it will eliminate more neighbors.

##### <span style="color:#7788f0">Pseudocode:</span>
```
flag = not decided // flag = true if the node is in MIS else false

while(flag is not decided){
	if(degree[v] = 0){
		flag = true 
	}
	else
		mark flag = true with a probability 1/(2* degree[v]) 
		
	if(flag = true){
		receive message from neighbor
		if there is a neighbor with flag = true and degree > degree[v]{
			flag = false 
		}
	}
	if(flag = true){
		notify neighbors.
	}
	else if(flag = false){
		if there is a neighbor with flag = true{
			delete itself and all its edges from the network.
			flag = false.
		}
	}
}
```

##### <span style="color:#7788f0">Analysis:</span>

###### Claim 1:
<span style="color:#69b5e9"><em>MIS Algorithm 1</em></span> runs in $O(\log n \log \Delta)$ with <span style="color:#69b5e9"><em>high probability</em></span>, where $\Delta$ is the maximum node degree.

###### Claim 2:
Consider any node $v$ in phase $i$ $(1 \leq i \leq \log \Delta)$ . One of the following events will happen - The status of $v$ will be determined or $d(v)$ will drop below $\dfrac{\Delta}{2^i}$ .

###### Proof:
For the sake of analysis, we divide the algorithm into phases - In the first phase, we will consider the nodes having degree between $[\Delta , \Delta /2)$ . In phase $i$, the nodes considered will have degree between $\left[\dfrac{\Delta}{2^{i-1}} , \dfrac{\Delta}{2^i}\right)$ . 

Thus, there will be $O(\log \Delta)$ phases. At the end of phase $i$ , the status of all nodes of degree higher than $\dfrac{\Delta}{2^i}$ would have been decided. 

Consider phase 1. We lower bound the probability that a status of $v$ will be determined in one round. This can be done in two ways:
1.  $v$ enters <span style="color:#69b5e9"><em>MIS</em></span> or
2.  a neighbor of $v$ enters <span style="color:#69b5e9"><em>MIS</em></span>.

We can lower bound the probability that a neighbor of $v$ enters <span style="color:#69b5e9"><em>MIS</em></span> as follows in two ways:
1.  A neighbor of $v$, say $w$ marks itself.
2.  At least one of $v$'s marked neighbors remain marked after the tie-breaking step.

The probability that none of the neighbors of $v$ enter the <span style="color:#69b5e9"><em>MIS</em></span> is at most
$$
\left(1 - \dfrac{1}{2 \Delta}\right)^{\Delta / 2} \leq e^{-1/4}
$$
This is because all the neighbors of $v$ have degree at most $\Delta$ and $d(v) \geq \frac{\Delta}{2}$ . Hence the probability that a neighbor of $v$ enters the <span style="color:#69b5e9"><em>MIS</em></span> is at least $1 - e^{-1/4}$ .

Let's bound the probability of the second way, given that at least one of the neighbor of $v$ has marked itself. Among all the neighbors of $v$ that are marked, consider the one with highest degree and highest priority as $w$. Now it is enough to focus on the neighbors of $w$ that are not in $\Gamma(v)$ since $w$ is the highest degree by assumption among $\Gamma(v)$ . 

Thus we can bound the probability of the following event, independent of the nodes in $N(v)$: The probability that at least one of the neighbors of $w$ (excluding those in $N(v)$) is marked is at most
$$
\displaystyle{\sum_{u \in \Gamma(w)} \dfrac{1}{2\cdot d(w)}} \leq \dfrac{1}{2}
$$
Thus the probability that none of its neighbors are marked is at least $\frac{1}{2}$ . Therefore, probability of both the events happening is $\beta = (1 - e^{-1/4})\cdot \frac{1}{2}$ . Let $k$ be the number of rounds this phase runs. Then probability that for a given node $v$ , both the events doesn't happen is $(1 - \beta)^k$ . 

Let $k = (C+1) \log_{1/(1-\beta)} n = O(\log n)$ , then by the <span style="color:#69b5e9"><em>union bound</em></span>, the probability that there is a node that $v$ such that its status isn't determined as well it's degree doesn't drop below $\frac{\Delta}{2}$ is at most
$$
p = n \cdot (1 - \beta)^k = \dfrac{1}{n^C}
$$
Thus, with probability at least $1-p$, all nodes would have either their status determined or their degree would have dropped below $\frac{\Delta}{2}$ . Similar argument can be applied for subsequent phases, by applying <span style="color:#69b5e9"><em>union bound</em></span> over all the $O(\log \Delta)$ phases, the status of all nodes will be determined with <span style="color:#69b5e9"><em>high probability</em></span> in $O(\log n \log \Delta)$ rounds.

#### <span style="color:#78bff2">MIS Algorithm 2:</span>

Lets cut the slack and dive into the pseudocode directly.
##### <span style="color:#7788f0">Pseudocode:</span>
```
flag = undecided
while(flag is undecided){
	if(degree[v] = 0):
		flag = true
	else
		flag = true 
		Choose a random real number uniformly and independently from [0 , 1]
		Let this number be rank[v]. Notify this to the neighbors
	if flag = true
		if lower-ranked neighbor has flag = true
			flag = undecided  // Tie-breaking step
	if flag = true 
		flag = true 
		send message to its neighbors
	if there is a neighbor with flag = true
		delete the node and all the edges from the network
		flag = false 	
}
```

##### <span style="color:#7788f0">Analysis:</span>
###### <span style="color:#ba7bee">Claim:</span>
In one iteration of the `while` loop of the algorithm, the expected number of edges deleted is <span style="color:#69b5e9"><em>at least half</em></span> the number of edges in the current graph.

###### <span style="color:#ba7bee">Proof:</span>
Let $F$ be the set of edges at the beginning of an iteration. Replace each undirected edge $(u , v)$ with two directed edges $u \rightarrow v$  and $v \rightarrow u$ . Call a node $u$ eligible with respect to $v$ if $u$ is the smallest ranked node among $N(u) , N(v)$. If $u$ is eligible w.r.t $v$, it will be in <span style="color:#69b5e9"><em>MIS</em></span> as it is the lowest ranked node among $N(u)$. 
$$
P(u ~\text{is eligibe w.r.t to }v) \geq \dfrac{1}{d(u) + d(v)}
$$
This probability can be derived directly using symmetry, since probability that at least one of the nodes from $N(u) , N(v)$ having the smallest number is $1$ , therefore the required probability is at least $\dfrac{1}{d(u) + d(v)}$ . This can also be shown using integration.

Let random variable $X(u \rightarrow v)$  denote the number of directed outgoing edges incident to $v$ that get deleted when $u$ is eligible w.r.t $v$ . $X(u \rightarrow v) \geq d(v)$ . Note that this is an undercounting, since we are not counting the edges removed when $u$ is deleted that are outgoing from $u$. But this is to ensure that we don't overcount the outgoing edges of $u$ that will be deleted when we calculate the total over all edges. More precisely, Let $X$ denote the total number of directed edges deleted in the iteration. Then

$$ 
X \geq \sum_{(u \rightarrow v) , (v \rightarrow u) ~\in F} X(u \rightarrow v) + X(v \rightarrow u)
$$
Note that $u$ is the lowest ranked among $N(v) , N(u)$ if it is eligible w.r.t $v$. This guarantees that no two neighbors of $v$ simultaneously try to delete the outgoing edges of $v$. 

By <span style="color:#69b5e9"><em>Linearity of Expectation</em></span>, the expected total number of directed edges deleted is
$$
E[X] \geq \sum_{(u \rightarrow v) , (v \rightarrow u) ~ \in F} E[X(u \rightarrow v)] + E[X(v \rightarrow u)]
$$
$$
\geq \sum_{(u \rightarrow v) , (v \rightarrow u) ~ \in F} \dfrac{d(v)}{d(u) + d(v)} + \dfrac{d(u)}{d(u) + d(v)} 
$$
$$
\geq |F|
$$
Since there $2|F|$ directed edges, the actual number of edges deleted is at least $\dfrac{|F|}{2}$.

###### <span style="color:#ba7bee">Claim:</span>
The algorithm terminates in $O(\log n)$ iterations with <span style="color:#69b5e9"><em>high probability</em></span>.

###### <span style="color:#ba7bee">Proof:</span>
Let $X$ be the number of edges remaining after $k$-th iteration. The expected number of edges remaining after $k$ iterations is at most $\dfrac{|E|}{2^k}$. Let $k = C \log n$.
Plugging this in, we get the expected number of edges remaining as
$$
E[X] = \dfrac{|E|}{n^C} 
$$
Using Markov's inequality,
$$
P(X \geq 1) \leq E[X]
$$
Since $|E| \leq n^2$ , therefore $P(X \leq 1) \leq \dfrac{1}{n^{C-2}}$ , we can conveniently chose $C = 4$ so that the probability that at least one edge will remain after $4 \log n$ iterations is at most $\dfrac{1}{n^2}$ .
## <span style="color:#d3a939">Exercises:</span>

1.  We have seen distributed algorithms for MIS and $3$-coloring.
	1. Show that given a $3$-coloring of the nodes it is possible to compute an MIS in $O(1)$ additional rounds. Does this mean that there exists an $O(\log^* n)$ round CONGEST algorithm for computing MIS on paths?
	2. Show that given any MIS of the path, it is possible to compute a $3$-coloring in $O(1)$ additional rounds. Does this mean that computing an MIS in the LOCAL model requires $\Omega(\log^* n)$ rounds? 


## <span style="color:#d3a939">Resources:</span>
- CS6851 Distributed Algorithms July-Nov 2025 Offering by Prof. Shreyas Pai.
- [Distributed Network Algorithms](https://drive.google.com/file/d/1axfgtgEmGvvWxBoaZhjwK1lVFrj_nV10/view) by Prof. Gopal Pandurangan (Chapter 6)