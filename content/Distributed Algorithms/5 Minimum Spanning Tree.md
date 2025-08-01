---
title: 5 Minimum Spanning Tree
---
---
<span style="color:#69b5e9"><em>Minimum Spanning Tree (MST)</em></span> problem is a <span style="color:#69b5e9"><em>global</em></span> problem since it needs at least $\Omega(D)$ rounds in a network of diameter $D$, i.e., the computation needs the entire graph to be traversed.

The <span style="color:#69b5e9"><em>MST</em></span> is an important and commonly occurring primitive in the design and operation of communication networks. In practice, the weights can be used to model delay, congestion etc. and hence an <span style="color:#69b5e9"><em>MST</em></span> gives a spanning tree that minimizes total delay, congestion etc. One of the most common applications of <span style="color:#69b5e9"><em>MST</em></span> is that can serve a backbone for efficient communication, e.g., it can be used naturally for broadcast.

We assume the clean network model and the <span style="color:#69b5e9"><em>synchronous CONGEST </em></span>model. At the end of the <span style="color:#69b5e9"><em>distributed MST algorithm</em></span>, each node will know which of its incident edges belong to the <span style="color:#69b5e9"><em>MST</em></span> and which do not. 

We also assume, for simplicity that all edge weights in the graph are distinct. This constraints the graph from having more than one <span style="color:#69b5e9"><em>MST</em></span>. This assumption is without loss of generality, since one can tag each edge weight with an additional identifier that can be used to break ties. 

As in <span style="color:#69b5e9"><em>centralized MST</em></span> algorithms, <span style="color:#69b5e9"><em>distributed MST algorithms</em></span> also rely on two important properties of a MST: 

1. <span style="color:#69b5e9"><em>Cut property</em></span>: A cut in a graph is a partition of the vertex set into two disjoint sets. The cut property states that, given any cut in a graph, the lightest (minimum weight) edge crossing the cut belongs to the <span style="color:#69b5e9"><em>MST</em></span> (due to the assumption of unique edge weights, there is a unique lightest edge crossing the cut.)
2. <span style="color:#69b5e9"><em>Cycle property</em></span>: Consider any cycle in the graph. The heaviest (maximum weight) edge in the cycle will not be in the <span style="color:#69b5e9"><em>MST</em></span>.

---
## <span style="color:#d3a939">Gallager-Humblet-Spira (GHS) Algorithm:</span>

We are given an undirected, connected, weighted graph $G = (V,E,w)$ . Let $n$ be the number of nodes and $m$ be the number of edges of $G$. Let $T$ be the MST on $G$. A <span style="color:#69b5e9"><em>MST fragment</em></span> $F$ of $T$ is defined as a connected subgraph of $T$ i.e., $F$ is a subtree of $T$. 

An <span style="color:#69b5e9"><em>outgoing edge</em></span> of a <span style="color:#69b5e9"><em>MST fragment</em></span> is an edge where one adjacent node to the edge is present in the fragment and the other is not. As an immediate consequence of the cut property, <span style="color:#69b5e9"><em>the minimum outgoing edge (MOE)</em></span> of a <span style="color:#69b5e9"><em>fragment</em></span> $F$ is an edge of the MST.

The <span style="color:#69b5e9"><em>GHS algorithm</em></span> operates in phases. In the first phase, the <span style="color:#69b5e9"><em>GHS algorithm</em></span> starts with each individual node as a <span style="color:#69b5e9"><em>fragment</em></span> by itself and continues till there is only one <span style="color:#69b5e9"><em>fragment</em></span> which is the <span style="color:#69b5e9"><em>MST</em></span>. All <span style="color:#69b5e9"><em>fragments</em></span> find their <span style="color:#69b5e9"><em>MOE</em></span> simultaneously in parallel.

In each phase, the algorithm maintains the following invariant: Each <span style="color:#69b5e9"><em>MST fragment</em></span> has a leader and all nodes know their respective parents and children. The root of the tree will be the leader. Initially, each node is a root node of the <span style="color:#69b5e9"><em>fragment</em></span>, and <span style="color:#69b5e9"><em>each fragment</em></span> is identified by the identifier of the root, called the <span style="color:#69b5e9"><em>fragment ID</em></span>. Each node in the fragment knows its <span style="color:#69b5e9"><em>fragment ID</em></span>.

### <span style="color:#8bd952">Description of GHS Algorithm:</span>

The <span style="color:#69b5e9"><em>GHS algorithm</em></span> assigns a <span style="color:#69b5e9"><em>level</em></span> to each <span style="color:#69b5e9"><em>fragment</em></span>, which is a non-decreasing integer with initial value 0. Furthermore, each <span style="color:#69b5e9"><em>fragment</em></span> with a non-zero level has an <span style="color:#69b5e9"><em>ID</em></span>, which is the ID of the core edge/root in the <span style="color:#69b5e9"><em>fragment</em></span>, which is selected when the <span style="color:#69b5e9"><em>fragment</em></span> is constructed. During the execution of the algorithm, each node can classify each of its incident edges into three categories:
- <span style="color:#69b5e9"><em>Branch</em></span> edges are those that have been determined to be part of the MST.
- <span style="color:#69b5e9"><em>Rejected</em></span> edges are those that have been determined not to be part of the MST.
- <span style="color:#69b5e9"><em>Basic</em></span> edges are all edges that are neither branch edges nor rejected edges.

In <span style="color:#69b5e9"><em>level-0 fragments</em></span>, each awakened node will do the following:

1. Choose its minimum-weight incident edge and mark that edge as a branch edge.
2. Send a message via the branch edge to notify the node on the other side.
3. Wait for a message from the other end of the edge.

The edge that is chosen by the two nodes it connects becomes the core edge, and is assigned <span style="color:#69b5e9"><em>level</em></span> 1.

In <span style="color:#69b5e9"><em>non-zero-level fragments</em></span>, a separate algorithm is executed in each level. This algorithm can be separated into three stages: <span style="color:#69b5e9"><em>broadcast</em></span>, <span style="color:#69b5e9"><em>convergecast</em></span>, and <span style="color:#69b5e9"><em>change core</em></span>.
#### <span style="color:#78bff2">Broadcast:</span>

The two nodes adjacent to the core broadcast messages to the rest of the nodes in the fragment. The messages are sent via the branch edge but not via the core. Each broadcast message contains the <span style="color:#69b5e9"><em>ID</em></span> and <span style="color:#69b5e9"><em>level</em></span> of the <span style="color:#69b5e9"><em>fragment</em></span>. At the end of this stage, each node has received the new <span style="color:#69b5e9"><em>fragment ID</em></span> and <span style="color:#69b5e9"><em>level</em></span>.

#### <span style="color:#78bff2">Convergecast:</span>

In this stage, all nodes in the <span style="color:#69b5e9"><em>fragment</em></span> cooperate to find the <span style="color:#69b5e9"><em>minimum weight outgoing edge</em></span> of the fragment. The message sent in this stage are <span style="color:#69b5e9"><em>opposite</em></span> direction to that of the broadcast stage. Each node upon receiving all the <span style="color:#69b5e9"><em>MOE</em></span> from its children will find the minimum among them along with its <span style="color:#69b5e9"><em>MOE</em></span> and send the result to its parent.

#### <span style="color:#78bff2">Change core:</span>

After the completion of the previous stage, the two nodes connected by the core can inform each other of the best edges they received. Then they can identify the minimum outgoing edge from the <span style="color:#69b5e9"><em>entire fragment</em></span>. A message will be sent from the core to the minimum outgoing edge via a path of branch edges. Finally, a message will be sent out via the chosen outgoing edge to request to combine the two <span style="color:#69b5e9"><em>fragments</em></span> that the edge connects. Depending on the <span style="color:#69b5e9"><em>levels</em></span> of those two <span style="color:#69b5e9"><em>fragments</em></span>, one of two combined operations are performed to form a new <span style="color:#69b5e9"><em>fragment</em></span>.

#### <span style="color:#78bff2">Finding the minimum-weight incident outgoing edge:</span>

As discussed above, every node needs to find its minimum weight outgoing incident edge after the receipt of a broadcast message from the core. If node $n$ receives a broadcast, it will pick its minimum weight basic edge and send a message to the node $n'$ on the other side with its fragment's ID and level. Then, node $n'$ will decide whether the edge is an outgoing edge and send back a message to notify node $n$ of the result. The decision is made according to the following:

1. $\text{F}_\text{ID}(n) = \text{F}_\text{ID}(n')$ , Then nodes $n$ and $n'$ belong to the same fragment
2. $\text{F}_\text{ID}(n) \neq \text{F}_\text{ID}(n')$ and $L(F) \leq L(F')$ , $F$ and $F'$ are corresponding fragments and $L(F)$ is the <span style="color:#69b5e9"><em>level of fragment</em></span> $F$, then nodes $n$ and $n'$ belong to <span style="color:#69b5e9"><em>different fragments</em></span>, so the edge is <span style="color:#69b5e9"><em>outgoing</em></span>.
3. $\text{F}_\text{ID}(n) = \text{F}_\text{ID}(n')$ and $L(F) > L(F')$ , Here we cannot make any conclusion. The reason is that two nodes may belong to the <span style="color:#69b5e9"><em>same fragment</em></span> already, but $n'$ has not discovered this fact yet due to the <span style="color:#69b5e9"><em>delay</em></span> of a broadcast message . In this case, the algorithm lets node $n'$ <span style="color:#69b5e9"><em>postpone</em></span> the response until its level becomes higher than or equal to the level it received from node $n$ .

#### <span style="color:#78bff2">Combining two fragments:</span>

Let $F$ and $F'$ be the two fragments that need to be combined. 
- <span style="color:#69b5e9"><em>Merge</em></span>: If both $F$ and $F'$ share a common MOE and $L(F) = L(F')$ then combine. The <span style="color:#69b5e9"><em>level</em></span> of the <span style="color:#69b5e9"><em>combined fragment</em></span> will be $L(F) + 1$ .
- <span style="color:#69b5e9"><em>Absorb</em></span>: If $L(F) < L(F')$ then combine. The <span style="color:#69b5e9"><em>combined fragment</em></span> will have its <span style="color:#69b5e9"><em>level</em></span> as $L(F')$.
- <span style="color:#69b5e9"><em>Wait</em></span>: Wait till the above rules apply.
##### <span style="color:#7788f0">Pseudocode:</span>

Variables:
- <span style="color:#69b5e9"><em>state</em></span> : <span style="color:#69b5e9"><em>sleep</em></span> , <span style="color:#69b5e9"><em>find</em></span> , <span style="color:#69b5e9"><em>found</em></span>
	- <span style="color:#69b5e9"><em>sleep</em></span> - The node is not initialized
	- <span style="color:#69b5e9"><em>find</em></span> - The node is currently helping its <span style="color:#69b5e9"><em>fragment</em></span> search for <span style="color:#69b5e9"><em>MOE</em></span>
	- <span style="color:#69b5e9"><em>found</em></span> - <span style="color:#69b5e9"><em>MOE</em></span> for the <span style="color:#69b5e9"><em>fragment</em></span> has been found
- <span style="color:#69b5e9"><em>status</em></span>$[q]$ : <span style="color:#69b5e9"><em>basic</em></span> , <span style="color:#69b5e9"><em>branch</em></span> , <span style="color:#69b5e9"><em>reject</em></span>
	- <span style="color:#69b5e9"><em>basic</em></span> - Edge is not yet determined
	- <span style="color:#69b5e9"><em>branch</em></span> - Edge is a part of the MST
	- <span style="color:#69b5e9"><em>reject</em></span> - Edge is not a part of the MST
- <span style="color:#69b5e9"><em>name</em></span> - Name of the fragment
- <span style="color:#69b5e9"><em>level</em></span> - <span style="color:#69b5e9"><em>Level</em></span> of the fragment
- <span style="color:#69b5e9"><em>parent</em></span> - Points towards the combining edge
- <span style="color:#69b5e9"><em>bestWt</em></span> , <span style="color:#69b5e9"><em>bestNode</em></span> , <span style="color:#69b5e9"><em>rec</em></span> , <span style="color:#69b5e9"><em>testNode</em></span> - temporary variables
 

Current node $p$ , Neighbor is $q$
###### <span style="color:#ba7bee">Initialization:</span>

```
1    pq is the least weight 
2    status[q] = branch 
3    level = 0 
4    state = found 
5    rec = 0 
6    send <connect , 0> to q
```

###### <span style="color:#ba7bee">Connecting Message:</span>

```
1     Receive <connect,L> from q:
2     if L < level then:
3 	      // Combine directly with root of p as the combined root
4 	      status[q] = branch
5	      send <initiate , level , name , state> to q
6
7     else if status[q] = basic then 
8	      wait
9
10    else 
11	    // Combine but increase the rank/level by 1
12	    send <initiate , level + 1 , pq , find> to q
```

###### <span style="color:#ba7bee">Initiate Message:</span>

```
1    Receive <initate , level' , name' , state'> from q
2	level = level'
3	state = state'
4	name = name'
5
6	bestNode = {} 
7	bestWt = INF
8	testNode = {}
9
10	for each r in adj[p]: // adj[p] - adjacency list of p
11		if (status[r] = branch && (r !=q)) then:
12			send <initiate , level' , name' , state'> to r
13
14	if state = find then:
15		rec = 0 
16		findMin()
```

###### <span style="color:#ba7bee">findMin() Function:</span>

```
1	if there exists a q in adj[p] such that status[q] = basic and weight of pq is minimal then:
2		testNode = q
3		send <test , level , name> to testNode
4
5	else
6		testNode = {}
7		report()
```

###### <span style="color:#ba7bee">Test message:</span>

```
1	Receive <test , level' , name'> from q
2
3	if level' > level then
4		wait
5	
6	if name = name' then
7		if status[q] = basic then
8			status[q] = reject
9
10		if q != testNode then
11			send <reject> to q
12
13		else
14			findMin()
15
16	else
17		send <accept> to q
```

###### <span style="color:#ba7bee">Accept and Reject messages:</span>

```
1	Receive <accept> from q:
2	testNode = {}
3	if weight of pq < bestWt then
4		bestWt = weight of pq
5		bestNode = q
6	report()
7	
8	Receive <reject> from q:
9	if status[q] = basic then:
10		status[q] = reject
11	
12	findMin()
```

###### <span style="color:#ba7bee">report() Function:</span>

```
1	report:
2	if((rec == Number such that status[q] == branch && q != parent) && testNode = {}):
3		state = found
4		send <report , bestWt> to parent
```

###### <span style="color:#ba7bee">Report Message:</span>

```
1	Receive <report , w> from q:
2	if q != parent then:
3		if w < bestWt then
4			bestWt = w 
5			bestNode = q
6		rec = rec + 1 
7		report()
8	
9	else:
10		if state == find:
11			wait
12	
13		else if w > bestWt then:
14			changeRoot()
15	
16		else if w = bestWt = INF:
17			stop
```

###### <span style="color:#ba7bee">changeRoot() Function:</span>

```
1	changeRoot():
2	if status[bestNode] = branch then 
3		send changeroot to bestNode
4	
5	else
6		status[bestNode] = branch
7		send <connect, level> to bestNode
8	
9	Receive changeroot:
10	changeRoot()
```

##### <span style="color:#7788f0">Message Complexity:</span>

Every node is rejected <span style="color:#69b5e9"><em>only once</em></span>, one <span style="color:#69b5e9"><em>test</em></span> message and one <span style="color:#69b5e9"><em>reject</em></span> message: Total $2|E|$ messages.

At every <span style="color:#69b5e9"><em>level</em></span>, a node sends/receives at most:
- receives - 1 <span style="color:#69b5e9"><em>initiate</em></span> message , 1 <span style="color:#69b5e9"><em>accept</em></span> message
- sends - 1 <span style="color:#69b5e9"><em>report</em></span> message , 1 <span style="color:#69b5e9"><em>changeroot/connect</em></span> message , 1 <span style="color:#69b5e9"><em>successful test</em></span> message

Since each node can be at at most $\log n$ <span style="color:#69b5e9"><em>levels</em></span> (Every level doubles the fragment size), therefore the message complexity is $2|E| + 5n \log n$ .

## Pipeline Algorithm:

The Pipeline algorithm is essentially an upcast algorithm, where we build a BFS tree over the graph and each node upcasts edges to the root of the BFS tree; the root ends up having (enough) global knowledge of the network topology and locally computes the MST and downcasts the MST edges to all nodes in the network. A naive algorithm would be to upcast all the edges in $\Theta(m)$ rounds. 

The main idea of the Pipeline MST algorithm is to filter the number of edges broadcasted so that the running time is reduced to $O(n)$ rounds. However, the message complexity can be as much as $\Theta(n^2)$ .

The pipeline algorithm uses the cycle property of MST to filter edges at the intermediate steps. Each node $v$ except the root $r$, maintains two lists of edges $Q$ and $U$. Initially, $Q$ only contains edges adjacent to $v$ and $U$ is empty. At each round, $v$ sends min-weight edge in $Q$ that does not create a cycle with the edges in $U$ to its parent and moves this edge from $Q$ to $U$. If $Q$ is empty, $v$ sends a terminate message to its parent. The parent after receiving an edge from a child, adds the edge in its $Q$ list. A leaf node starts sending edges upwards at round $0$ . An intermediate node starts sending at the first round after it has received at least one message from each of its children. 

### Pseudocode:

```
1. Build a BFS Tree B in G. Let r be the root of B
2. Each node v , except the root r , maintains two list of edges, Q(v) and U(v)
3. A leaf node starts sending edges upwards at round 0. An intermediate node starts sending at the first round after it has received at least one message from each of its children.
4. Initially Q(v) contains only edges adjacent to v and U(v) is empty. At each round, v sends the minimum-weight edge in Q(v) that does not create a cycle with the edges in U(v) to its parent and moves this edge from Q(v) to U(v). Any edge that creates a cycle with edges in U(v) is deleted from Q(v). If Q(v) is empty, v sends a terminate message to its parent. The parent after receiving an edge from a child adds the edge in its Q(v) list.
5. The root r computes the MST locally among the edges it hears from its children . The solution is then broadcast over tree B to all nodes.

```

### Analysis:

We make two observations
1. The edges reported by each intermediate vertex to its parent in the tree are cycle-free.
2. Each vertex starts sending messages upwards at round $L(v) = \text{height}(T(v))$ .

Consider an intermediate vertex $v$ at height $h$ that has still not terminated its participation in the algorithm, at round $t$, for some $t \geq h$. A child is active if it has not terminated yet.

#### Claim:
1. For each child $u$ of $v$ that is still active at round $t$ , $Q(v)$ at the beginning of round $t$ contains at least one edge.
2. If $v$ sends to its parent an edge of weight $w_0$ at round $t$, then all of the elements $v$ was informed at round $t-1$ by its active children were of weight $w_0$ or larger.
3. If $v$ sends to its parent an element of weight $w_0$ at round $t$ , then any later element it will learn is of weight $w_0$ or larger.
4. Any non-root node $v$ sends elements in nondecreasing weight order to its parent; it sends the elements in a continuous fashion till it terminates.

#### Proof:
The proof is by induction on the height of the tree.
- Base case: Trivially holds for leaves
- Induction Step: Consider an intermediate vertex $v$ at height $h$  and assume that the claims hold for each of its children.
	- Let $A_v$ be the set of $k$ elements sent by $v$ to its parent during the first $t - h$  rounds. The set of edges in $A_v$ are cycle-free , by the line 4 in algorithm (Sent edges are in $U(v)$ and edge which is going to be sent doesn't form cycle with $U(v)$).  Consider an active child $u$ of $v$. Let $A_u$ be the set of elements sent by $u$ to $v$ up to round $t-1$. $u$ has continuously transmitted to $v$ , since round $L(u) \leq h-1$ . Hence $|A_u| \geq k+1$ . Thus there exists some edge $f \in A_u - A_v$ such that $A_u \cup \{f\}$ is cycle-free. This element belongs to $Q(v)$.
	- Consider any active child $u$ of $v$. Let $f$ be the element sent by $u$ on round $t-1$ . Let $f'$ be some element by $u$ at some round $t' \leq t-1$ and is still in $Q(v)$ at round $t$ . Then $w(f) \geq w(f') \geq w_0$ .

#### Running Time and Message Analysis:
The root starts getting messages at time $\text{height}(T)$ . The root receives at most $n-1$ elements from each of its children. The time to know all edges is $O(n - 1 + \text{height}(T))$ . If we consider the additional broadcasting, the total time is $O(n)$. 
In the worst case, each node can $\Theta(n)$ edges upward, and hence the overall message complexity is $O(n^2)$ .

## Garay-Kutten-Peleg (GKP) Algorithm:

Lets look at a distributed MST algorithm that runs in $O(D + \sqrt n \log^* n)$ time.  The GKP algorithm consists of two parts: it combines the GHS algorithm 


## Resources:

- [Advanced Distributed Systems Lectures](https://www.cse.iitd.ac.in/~srsarangi/courses/2021/col_819_2021/index.html) by Prof. Smruti Sarangi (GHS Algorithm)
- [Distributed Network Algorithms](https://drive.google.com/file/d/1axfgtgEmGvvWxBoaZhjwK1lVFrj_nV10/view) by Prof. Gopal Pandurangan (Chapter 7)