---
title: 6 Tree Algorithms
date: 2025-07-10
---
## <span style="color:#d3a939">Broadcast: </span>

Lets look at the following problem:

Given a network $G = (V , E)$ and a source node $s$ , send a <span style="color:#69b5e9"><em>message</em></span> $M$ from $s$ to all nodes in $V$. 
### <span style="color:#8bd952">Flooding Algorithm:</span>

This algorithm is pretty straightforward. Every vertex $v$ $\neq s$ upon receiving $M$ for the first time, forwards it on every other edge. More like a BFS with visited array, where receiving the message makes the visited flag to be set true and the node no longer sends the message and stops. 

#### <span style="color:#78bff2">Pseudocode:</span>
```
if v == s then
	send message M to all neighbors and stop
else if M is received for the first time then
	send M to all neighbors and stop
```

#### <span style="color:#78bff2">Analysis:</span>
##### <span style="color:#7788f0">Claim:</span>
The flooding algorithm is correct i.e., all nodes eventually receive the message from the source node. The message complexity is $\Theta(|E|)$ and the time complexity is $D$ , where $D$ is the diameter of $G$.

##### <span style="color:#7788f0">Proof:</span>
Let $v$ be any node. We use induction on $t$ to show that after $t$ time units, the message has already reached every vertex in $\mathrm{ball}_G(s , t)$ . 
- <span style="color:#69b5e9"><em>Base case:</em></span> At $t = 0$ this is trivially true. 
- <span style="color:#69b5e9"><em>Induction Step:</em></span> We assume the hypothesis is true at time $t$. It follows at time $t+1$ , all neighbors of nodes at distance $t$ , which are at distance $t+1$ , will receive the message.

The message complexity follows from the fact that each edge delivers the message <span style="color:#69b5e9"><em>at least once</em></span> and at most twice (one in each direction). 

The time complexity is clearly bounded by the <span style="color:#69b5e9"><em>diameter</em></span>, which is the maximum distance between any two nodes in $G$.

### <span style="color:#8bd952">Lower bound for Broadcast:</span>

#### <span style="color:#78bff2">Claim:</span>
Any distributed algorithm for broadcast has a message complexity of $\Omega(n)$ and time complexity of $\Omega(D)$.

#### <span style="color:#78bff2">Proof:</span>
- <span style="color:#69b5e9"><em>Message Complexity:</em></span> Every node has to receive the message so at least $n-1$ messages are needed. Note that this is not a tight lower bound for broadcast.
- <span style="color:#69b5e9"><em>Time Complexity:</em></span> For any source node $s$ , there exists a node $u$ which is at a distance of at least $\Omega(D)$ from $s$. Note that this is a tight lower bound for broadcast.

### <span style="color:#8bd952">Tree Broadcast:</span>

A spanning tree-based broadcast from a source node $s$ proceeds as follows. The root $s$ sends the message to its neighbors. When a node $v$ receives the messages for the <span style="color:#69b5e9"><em>first time</em></span>, it forwards it along the <span style="color:#69b5e9"><em>tree edges</em></span> only except to the parent node. If $v$ receives the message again, it ignores as usual.

#### Claim:
Given a graph $G$ with spanning tree $T$ rooted at $s$, the message complexity of broadcast from $s$ is $n-1$ and time complexity is $\mathrm{height}(T)$ .

This is just substitution of $m = n-1  ~ , ~ D \geq \mathrm{height}(T)$ in the previous results.

## <span style="color:#d3a939">Convergecast</span>

This can be viewed as the opposite to <span style="color:#69b5e9"><em>broadcast</em></span>, where the all the nodes send the information to the root of a spanning tree in an <span style="color:#69b5e9"><em>upwards</em></span> fashion. 

Let $T$ be a spanning tree rooted at node $r$ . Let's say we each node has a value $a_v$ and we need the sum of values of the network. Then we can run the following algorithm. Start from the leaf nodes. In the first round, each leaf node sends its value to the parent. The parent nodes sum up all the values it receives and adds its own value as well. This value is sent up when it gets information from all the children This process terminates at the root $r$. The time taken $O(\mathrm{height}(T))$ .

![[Pasted image 20250711014718.png]]

Consider the flooding algorithm. Suppose the source wants to know when the algorithm terminated. First, the flooding can be used to construct a spanning tree rooted at the source. Let $T$ be the spanning tree. Each leaf node in the tree will send an <span style="color:#69b5e9"><em>echo</em></span> message to its parent. Each internal node, when it receives from all the children will send an <span style="color:#69b5e9"><em>echo</em></span> message to the parent. Once the root receives the <span style="color:#69b5e9"><em>echo</em></span> messages from all its children, it will know that the broadcast algorithm has terminated successfully.

### <span style="color:#8bd952">Pipelining:</span>

Consider the following problem: Each node has a list of $k$ values i.e. each node has values $[a_i(1) , a_i(2) , ... , a_i(k)]$ , and the goal is to compute the list of component wise sums

$$
\left[\displaystyle{\sum_{i = 1}^{n} a_i(1) , \sum_{i = 1}^{n} a_i(2) , ... , \sum_{i = 1}^{n} a_i(k)}\right] 
$$
at some source $s$ . 

Instead of a straightforward algorithm of performing <span style="color:#69b5e9"><em>convergecast</em></span> $k$ times for each element in the list ($O(k \cdot \mathrm{height}(T))$ time), we can do much better using something called <span style="color:#69b5e9"><em>pipelining</em></span>. 

Each node when it receives the value send the aggregated values component by component to their respective parent. More formally, for the first $k$ rounds, the leaf nodes will send the list values one by one. An intermediate node $v$ would receive all the values of $j$th component in the round $j + \mathrm{height}(v)$ . At this time, it will send the aggregated values along with its value to its parent. Using an inductive argument, it could be shown that the total number of rounds would be $O(k + \mathrm{height}(T))$ .

## <span style="color:#d3a939">Upcast</span>

Consider the following problem: We are given $k$ items distributed arbitrarily on the nodes of a network $G = (V , E)$. A node can have zero , one or more items. We would like to collect all these items at a node $s$ $\in V$ . Assume that item size is small, has a unique ID and can be represented in $O(\log n)$ bits.

We cannot do <span style="color:#69b5e9"><em>convergecast</em></span> as aggregating items isn't possible. Instead we modify the <span style="color:#69b5e9"><em>convergecast</em></span> and do it individually for each item, which would be inefficient and would take $O(k \cdot \mathrm{height}(T))$ time.

Consider the following algorithm - At any round, each node does the following: Among the set of items that it currently has, send the item with highest ID (<span style="color:#69b5e9"><em>highest priority</em></span>) to its parent. The sent item is removed from the set of items it currently has. The main idea of this algorithm is to use the ID to break ties. This is useful while analyzing the algorithm.

![[Pasted image 20250711014701.png]]

### <span style="color:#8bd952">Claim: </span>
The above algorithm <span style="color:#69b5e9"><em>upcasts</em></span> all the items to the root and takes $O(k + \mathrm{height}(T))$ time.

### <span style="color:#8bd952">Proof:</span>
Idea: Note that the highest priority element is <span style="color:#69b5e9"><em>never delayed</em></span> in any round i.e., it is sent to the root in $O(\mathrm{height}(T))$ time to the root. The second highest element, can be delayed only by the highest element, and that too <span style="color:#69b5e9"><em>only once</em></span>. Let $x$ be the highest priority element and $y$ is the second highest priority element. The previous statement is true since once $y$ gets delayed and $x$ moves up, there is no other element to delay both $x$ and $y$, as well as $x$ can't delay as it has moved up already.

Consider the $j$-th highest priority element. It can possibly delayed by the items that are at <span style="color:#69b5e9"><em>higher priority</em></span> than this element. Let $j'$ be the priority of the element of the lowest priority that delays $j$-th priority element. Note that once this happens, it is never delayed again by $j'$ and any further delay can only be attributed to an item that is ranked higher than $j'$, let's say $j''$.  This is because $j''$ can delay $j'$ and make $j$ catch up with $j'$ , This would incur an additional delay, but that delay could be attributed to $j''$ . Thus the $k$-th element would at most incur a delay of $k$.

## <span style="color:#d3a939">Downcast</span>

Inverse problem of <span style="color:#69b5e9"><em>upcast</em></span>. Instead of collecting things to a single node, we distribute things from a single node to all nodes. We can do this by an algorithm very similar to the one used in <span style="color:#69b5e9"><em>upcast</em></span>. When sending message to neighbors, choose the ID with the lowest priority and send it. This can be done in $O(k + \mathrm{height}(T))$ time.

## <span style="color:#d3a939">BFS Spanning Tree</span>

The Flooding algorithm could be modified to convert the network into a <span style="color:#69b5e9"><em>breadth-first spanning tree</em></span> only network. In other words, once the algorithm terminates, each node will be aware of its parent as well its children in the spanning tree. Note that the root node will not have any parent and leaf nodes won't have any children.

### <span style="color:#8bd952">Distributed BFS Tree construction:</span>

Source node $s$ sends an <span style="color:#69b5e9"><em>invite</em></span> message inviting its neighbors to be its children. When a node $v$ receives an <span style="color:#69b5e9"><em>invite</em></span> message from one or more neighbors for the first time, it will respond exactly to one such <span style="color:#69b5e9"><em>invite</em></span> message. It does by sending an <span style="color:#69b5e9"><em>accept</em></span> message to its parent. Any <span style="color:#69b5e9"><em>invite</em></span> message in the later rounds is ignored. Very similar to visited array concept used in graph traversal. 

#### <span style="color:#78bff2">Pseudocode:</span>
```
if v == s then
	send "invite" message to all neighbors
	designate all neighbors as children
else if when "invite" message is received for the first time, then
	send "accept" to exactly one neighbor p , make p as the parent.
	send "invite" message to all neighbors except p.
	when "accept" message is received from any neighbor u, make u as a child.
```

##### <span style="color:#7788f0">Analysis:</span>
Correctness analysis can be done using induction. Message and Time complexity is asymptotically equivalent to the message complexity of Flooding i.e., $O(|E|) , O(D)$.

#### <span style="color:#78bff2">Termination Detection:</span>

This is a <span style="color:#69b5e9"><em>global</em></span> knowledge i.e., requires knowledge about status of all the nodes in the network. As we saw in the <span style="color:#69b5e9"><em>convergecast</em></span>, each node can just echo back once all its children has echoed back. As for the leaf nodes, leaf nodes will <span style="color:#69b5e9"><em>echo</em></span> back immediately and leaf nodes can self identify themselves by identifying that no <span style="color:#69b5e9"><em>accept</em></span> message has arrived in the subsequent round. Once the root node receives the <span style="color:#69b5e9"><em>echo</em></span> from all its children, a broadcast can be done to all the other nodes, so that the termination message is spread over the network.

## <span style="color:#d3a939">Information Spreading</span>

Given a network $G = (V , E)$ of $n$ nodes and $m$ edges, and a set of $k$ tokens arbitrarily distributed among the $n$ nodes, we need to disseminate the $k$ tokens to all the nodes. This problem can be viewed as a generalization of upcast, here we need to send it to all nodes instead of the root node only. One application of this is to acquire <span style="color:#69b5e9"><em>global knowledge</em></span> of the network, when every node wants to know the <span style="color:#69b5e9"><em>global topology</em></span>.

Consider the following algorithm: Find a BFS tree. Then <span style="color:#69b5e9"><em>upcast</em></span> all $k$ items to the root of the tree. Then we <span style="color:#69b5e9"><em>downcast</em></span> the items to all the nodes. Overall time complexity of this algorithm would be $O(D) + O(D + k) + O(D + k) = O(D + k)$. 

For the message complexity, it is clear that $\Omega(nk)$ is a lower bound, as only one message is passed each round and each node has to get $k$ messages. Overall message complexity of this algorithm would be $O(m) + O(kD) + O(nk) = O(m + nk)$ . 

## <span style="color:#d3a939">Exercises</span>

Will be added soon
