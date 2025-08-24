## [[3 Coloring Algorithms|3 Coloring Algorithms]]

#### Design a $O(\log^* n)$ round algorithm that works on any path (not given a consistent orientation of the edges). 

A more generalized version of this problem would be $3$-coloring an undirected path. If we have the algorithm $A$ for the actual question, then we can just solve the undirected path by just running $A$ after randomizing the orientation. This can be done in multiple ways, one way would be, for each pair of neighboring nodes, orient the edge towards the node with higher ID. This will result in a path graph but not necessarily consistent.

Now, given such path, we define source nodes as the nodes with no incoming edges and sink nodes as the nodes with no outgoing edges. By definition, sink nodes cannot be neighbors of source nodes (and vice versa). Color all source nodes with the same color (Let's say 0), color all the sink nodes with the same color (Let's say 1). Now we can run the $O(\log^* n)$ color-reduce algorithm we saw for $3$-coloring directed path on the remaining nodes.

This will work (with small subtleties that can be fixed locally) in a way, since by fixing the colors of the source and sink nodes, we essentially partition the path into sets of consistent directed paths. If at the start, none of the nodes had $0$ or $1$ as their unique ID, then this is complete and will produce a valid $3$-coloring. In the case where one of the nodes with ID $0$ or $1$ turns out to be a neighbor of a source or sink node respectively, We can fix this locally (by taking min) after color reduce or we can just initialize this at start to some max value and run the color reduce without any issues.

## [[5 Maximal Independent Set|5 Maximal Independent Set]]

#### We have seen distributed algorithms for MIS and $3$-coloring.
##### 1. Show that given a $3$-coloring of the nodes it is possible to compute an MIS in $O(1)$ additional rounds. Does this mean that there exists an $O(\log^* n)$ round CONGEST algorithm for computing MIS on paths?

We can compute MIS in $3$-additional rounds, given a $3$-coloring of the nodes. First, we include all the nodes that are colored $0$ directly into the MIS. Then we include all the nodes that are colored $1$ which do not have any of their neighbor included in MIS. Then we include all the nodes that are colored $2$ which do not have any of their neighbor included in MIS. This is straightforward, that the included nodes must form MIS, since the $3$-coloring is valid, two adjacent nodes are never included in the same round, and across rounds as well (by the algorithm definition). This set is also maximal, by the algorithm setting.

##### 2.  Show that given any MIS of the path, it is possible to compute a $3$-coloring in $O(1)$ additional rounds. Does this mean that computing an MIS in the LOCAL model requires $\Omega(\log^* n)$ rounds? 

We can compute the $3$-coloring from MIS in the following way. Color all the nodes in the set to color $0$. Let's now consider only the nodes that are not colored. Either they form pairs or they are isolated. The chain of uncolored nodes cannot extend more than $2$ since the set is maximal (if it were so, we can include the central node into the set, which is contradiction). 

For the isolated nodes, color them all by color $1$. For the pair nodes, color the highest ID node with $1$ and the lowest ID node with $2$. This is guaranteed to be a valid $3$-coloring since the nodes with color $0$ make it impossible for two adjacent nodes to have same color by construction. 

Given the lower bound of $\Omega(\log^* n)$ for $3$-coloring, If there was an algorithm $A$ which could compute MIS in $o(\log^* n)$, then such an algorithm could be used to compute $3$-coloring in $o(\log^* n)$ . But we have proved the lower bound already, so $A$ cannot exist. Thus, this means that computing an MIS on directed paths requires $\Omega(\log^* n)$.