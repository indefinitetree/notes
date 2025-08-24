
Distributed algorithms present theoretical computer science from a different perspective. 

Let's start with an example:
	Imagine there is a graph with a large set of nodes - The nodes could represent anything. Maybe a network of computers exchanging information. Maybe a group of cells in a multicellular organism which exchange biochemical information continuously.

Given this graph, we might need to compute some information, let's say we need to find a <span style="color:#69b5e9"><em>matching</em></span> in this graph, or maybe we need to find a <span style="color:#69b5e9"><em>coloring</em></span> of the graph that uses least number of colors (Classic graph theory problems).

Coloring problems though why? How is it useful in real-life situations? Let's take the network of computers example. A <span style="color:#69b5e9"><em>coloring</em></span> here would give a schedule to exchange information. Choose an ordering over the set of colors $C$ . Then iterate over it. At time $t$ , all the computers which are marked with color $c_t$  can be made active. Such a set of computers of the same color $c_t$ would form an <span style="color:#69b5e9"><em>independent set</em></span>. Since no two nodes are neighbor in such an <span style="color:#69b5e9"><em>independent set</em></span>, therefore the active computers can safely do whatever they want without disrupting the nearby computers (since they are inactive).

A general overview of computation problems that we are interested would be - Given a graph, the nodes need to work together (in the sense, exchange information) to solve some computation problem. Usually it is a graph problem - Finding a spanning tree, finding a proper vertex coloring, etc.
## <span style="color:#d3a939">Distributed Problems</span>

When we talk about distributed problems, there are few assumptions. Initially, each node is <span style="color:#69b5e9"><em>only aware of itself</em></span> (For example, the color of the node itself). The final goal is such that each node knows its own part of the solution for a given computation problem (In the case of coloring, the final color the node should have). This is already enough for each node to know what to do. For example, if the node knows that its final color is $c_t$ , then time it should be active is time slot $t$ .

This is the key difference between sequential (classical) computing and distributed computing. In classical computer science problems, we assume that the whole input in stored in <span style="color:#69b5e9"><em>one place</em></span> (For example, the complete graph), we have to process it and produce a whole output (the complete color mapping for each node in the graph).

In distributed computing, each node has no information about the whole graph, except the node itself. It knows about its neighbors and it can exchange messages only with those neighbors to explore the graph. This could be done for every node and this process could be done iteratively until every node of the graph has the complete information about the graph, but this might not be feasible in terms of communication. By communication, we mean the exchange of information between the computers. Each exchange of information could be viewed as a communication round, where each node exchanges messages with its own neighbors once. We want to minimize the number of communication rounds. 

Indeed, there is an assumption here that there is some <span style="color:#69b5e9"><em>ulterior superpower/super machine</em></span> that knows the whole input (i.e., the global structure of the graph), but we don't pay much attention to this while talking about distributed problems. Also, distributed problems are differently from parallel computing problems. Parallel computing problems also have the full input/output stored at one place, except the computation is parallelized. 

In other areas of computer science, we have a bird's eye view of what the computation problem's input looks like, while in distributed algorithms, we only have an insider's view. We should be able to produce our own output (irrespective of other outputs) based on the information exchanged in the surroundings and so does every node should. This introduces us to the concept of <span style="color:#69b5e9"><em>locality</em></span>. Fast distributed algorithms are <span style="color:#69b5e9"><em>necessarily </em></span>highly localized. 

By "fast" , here we talk about the number of <span style="color:#69b5e9"><em>communication</em></span> rounds as well along with the classical time complexity and space complexity (computation rounds). Just how time and space are viewed as a resource, <span style="color:#69b5e9"><em>communication</em></span> could also be viewed as a resource in distributed problems. This is because in general networks of computers, communication step (exchanging information) becomes the bottleneck over the computation steps. To give a perspective on how costly a communication is, getting one bit from another computer in the same local network approximately takes the same time as computing one billion arithmetic operations inside a normal computer.  

Distributed algorithms heavily rely on <span style="color:#69b5e9"><em>locality</em></span>. Before designing an algorithm, one should be aware of which graph problems are <span style="color:#69b5e9"><em>local</em></span> and which graph problems are <span style="color:#69b5e9"><em>global</em></span>.  <span style="color:#69b5e9"><em>Local</em></span> problems can be solved so that each node only looks at its neighbors, while <span style="color:#69b5e9"><em>global</em></span> problems need the whole input graph to be available for the computation. This would also answer the question of which problems could be done using smaller number of communication rounds and which problems necessarily require larger number of rounds.

Such answers will be useful in understanding nature and fundamental limitations of any other system consisting of interacting entities such as social networks, job markets etc.

## <span style="color:#d3a939">Definitions and Terminologies:</span>

Before we move on to the Algorithms, there are few terminologies and assumptions that should be made clear about the distributed network model we consider.
1.  The network model is a <span style="color:#69b5e9"><em>connected undirected graph</em></span> - It could be weighted or unweighted

2.  Local communication - Nodes can communicated directly(only) with their neighbors through the edges. There are two types of communication.
	- <span style="color:#69b5e9"><em>Local unicast</em></span> - Nodes can send different messages to each of its neighbors (more suitable to wired networks).
	- <span style="color:#69b5e9"><em>Local broadcast</em></span> - Nodes send the same message to all of its neighbors in any step (feature in wireless networks).

3.  Synchrony - Two important models can be distinguished based on processor synchronization.
	- <span style="color:#69b5e9"><em>Synchronous model</em></span> - Each processor has an internal clock and the clocks are <span style="color:#69b5e9"><em> synchronized</em></span>. We assume the processor speeds are <span style="color:#69b5e9"><em>uniform</em></span> and each processor takes the same amount of time to perform the same operation. Computation proceeds in lock-step in a series of discrete rounds (time steps). In each round, each processor (node) can do some local (internal) computation and can also send/receive messages. To be concrete, we assume that at the beginning of a round, a node receives messages (if any) from its neighbors via its incident edges.
	 
	- <span style="color:#69b5e9"><em> Asynchronous model</em></span> - No assumptions are made about the internal clocks. We assume that messages arrive in the same order they are sent (FIFO). Algorithms designed for the synchronous model could be transferred to asynchronous model by means of a tool called the <span style="color:#69b5e9"><em>synchronizer</em></span>.

4.  Local knowledge - Two models
	 - <span style="color:#69b5e9"><em>KT0</em></span> - (**K**nowledge of all nodes is restricted **T**ill radius 0) also known as <span style="color:#69b5e9"><em>clean network model</em></span>. Standard model that is typically used. In this model, each node has a port associated with an incident edge (each having a port number). Each node only knows about its own port number and that an edge goes out of it, but nothing about the other endpoint of the edge.
	 
	 - <span style="color:#69b5e9"><em>KT1</em></span> - Here one can assume that the nodes have initial knowledge of their neighbors, especially their IDs.

5.  CONGEST vs LOCAL 
	 - <span style="color:#69b5e9"><em>CONGEST</em></span> - The size of each message sent per round is small, typically of size $O(\log ~n)$ , where $n$ is the size of the network. This is a reasonable bound as this is at least required to send the unique address of a node. This model captures the inherent bandwidth restriction that is present in real-world networks.
	 
	- <span style="color:#69b5e9"><em> LOCAL</em></span> - There is no restriction on the size of message. This model is useful in focusing on <span style="color:#69b5e9"><em>locality</em></span> issues in distributed computing.

6.  Operation - Usually, each node is assumed to operate on the <span style="color:#69b5e9"><em>same instance</em></span> of the algorithm. However, depending on the local information, each node can have its own behavior (due to randomness or unique ID or the information sent by other nodes).

## <span style="color:#d3a939">Resources:</span>

- [Distributed Algorithms 2020](https://jukkasuomela.fi/da2020/) by Prof. Juho Hirvonen and Prof. Jukka Suomela (Chapter 1).
