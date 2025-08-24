## <span style="color:#d3a939">Coloring with Randomized Algorithms</span>

### <span style="color:#8bd952">Randomized Algorithms:</span>

Randomized Algorithms can be classified into two types:

1. Las-Vegas Algorithms: Algorithms which provide correct solutions (i.e., doesn't fail on any input), but running time of the algorithm is a random variable and is expressed in terms of expected runtime.
2. Monte-Carlo Algorithms: Algorithms whose running time complexity is determined/fixed but the algorithm doesn't necessarily produce correct solutions. The algorithm's success/failure is a random variable. It could be bounded by a probability. A common term used is "Algorithm runs with high probability". This means that the probability of algorithm providing a incorrect output (failure) can be bounded as $\leq  \dfrac{1}{n^C}$ , where $C$ is a constant related to running time (usually hidden due to asymptotic notation).

### <span style="color:#8bd952">Union-Bound:</span>

Let $A_i$ be the event that node $i$ fails to compute the solution properly. Then the union bound tells us that
$$
P\left[\bigcup_{i = 1}^{n} A_i\right] \leq \sum_{i=1}^{n} P \left[ A_i \right]
$$
This is straightforward from the inclusion-exclusion principle, where we drop the higher order terms.
### <span style="color:#8bd952">3-coloring Randomized Algorithm:</span>

Here is a fairly straightforward algorithm. Each node has a flag $u_a \in \{0 , 1\}$ , indicating whether it has stopped or not, and value $v_a$. Once $u_a$ is $1$ , the node outputs $v_a$. 

In each step, every node with its flag set to $0$ , picks a new color $c$ from $\{1 , 2 , 3\}$ uniformly at random. Then each node sends it current color to its neighbor. If $c$ is different from that of its neighbors, then the flag is set to $1$ and the node stops. Otherwise this continues.

It is easy to see that in each step, a node $a$ will stop with probability $1/3$ . Fix a positive constant $C$. Let 
$$
k = (C + 1) \mathrm{log}~_{3/2} ~ n
$$
where $n$ is the number of nodes in the graph. Now if we run this algorithm for $k$ steps, the probability that a given node $a$ has not stopped is 
$$
\left(\dfrac{2}{3}\right)^k = \left(\dfrac{1}{n}\right)^{C+1} 
$$
By the union bound, the probability that there is a node that has not stopped is at most 
$$
p = \dfrac{1}{n^C}
$$

Thus, with probability at least $1 - p$ , all nodes have stopped after $k$ steps. For any given constant $C$ , there is an algorithm that runs for $k$ = $O(\mathrm{log} ~ n)$ rounds and produces a proper 3-coloring of a path with probability $1 - \dfrac{1}{n^C}$ . 

### $\Delta$+1 Randomized Coloring algorithm:

#### Outline:
- Initially every node is in sleep state.
- At $i$-th iteration:
	- Each node $u$ wakes up with a probability $0.5$ .
	- If a node is awake, it picks up $c_u \in L_u$ uniformly where $L_u$ is the set of admissible colors. Initially $L_u = \{1 , 2 , ... , \Delta +1\}$. Send $c_u$ to the neighbors.
	- If $c_u \neq c_v ~\forall ~ v \in N(u)$ , $u$ fixes $c_u$ and send that it is fixed to its neighbor. 
	- All nodes then update $L_u$ by removing all the fixed colors.
	- Go back to sleep.

It is straightforward that the algorithm computes a proper coloring, since the algorithm runs as long as there is a conflict in the coloring and terminates only when all the nodes are fixed which is when the graph has a proper coloring.
#### Analysis:
##### Claim: 
The algorithm terminates in $O(\log n)$ rounds with high probability.

##### Proof:
Let $\text{fix}(u)$ be the event such that $c_u$ is not picked by any neighbor of $u$. Let $N_u$ be the neighborhood of $u$ that has not yet been fixed. Consider a $v \in N_u$ .  
$$P[u~  \text{fixes its color at}~ i~\text{-th iteration}] = P[\text{fix}(u) ~ | ~ u ~ \text{wakes up}] \cdot P[u ~ \text{wakes up}] 
$$
Probability that $v$ picks $c_u$  = Probability that $v$ picks $c_u$ given $v$ wakes up + Probability that $v$ picks $c_u$ given $v$ doesn't wake up .
$$
P[v ~\text{picks}~ c_u] \leq \dfrac{1}{2} \cdot 1 ~ + \dfrac{1}{2} \cdot 0 \implies P[v ~ \text{does not pick} ~ c_u] > \dfrac{1}{2}
$$
$$
P[u ~ \text{picks} ~ c_u] = \dfrac{1}{|L_u|} 
$$
$$
P[v ~\text{does not pick the same color as} ~ u] \leq \dfrac{1}{2 | L_u|}
$$
Therefore, by union bound, 
$$
P[u ~ \text{fixes its color}] \geq \dfrac{|N_u|}{2 |L_u|} \geq \dfrac{1}{4}
$$
Let $f(u,i)$ be the event that $u$ colors itself by iteration $i$ . 
$$
P[f(u,i) ~ | ~ \overline f(u , i-1)] \geq \dfrac{1}{4}
$$
$$
P[f(u , i)] =  P\left[\bigcup_{j = 1}^i f(u , j) \right] = 1 - P\left[\bigcap_{j = 1}^i \overline f(u , j)) \right]
$$
Using $P[X_1 \cap X_2 \cap ... \cap X_n] = P[X_1] P[X_2 ~ | ~ X_1] .. P[X_n ~|~ X_1 \cap X_2 \cap ... \cap X_{n-1}]$  and $$\bigcap_{j=1}^i \overline f(u , j) = \overline f(u , i-1)$$ Using these two, we get 
$$
P[f(u , i)] = 1 - (P[\overline f(u , 1)] \cdot P[\overline f(u , 2) ~ | ~ \overline f(u , 1)] ~ ... )
$$
$$
= 1 - \left(\left( \Pi_{j=2}^{i} ~ P[\overline f(u , j) ~|~ \overline f(u , j-1)] \right) \cdot P[\overline f(u , 1)] \right)
$$
$P[\overline f(u , 1)] = 1$ and $P[\overline f(u , j) ~|~ \overline f(u , j-1)]$ is complement of $P[f(u,i) ~ | ~ \overline f(u , i-1)]$ . Therefore
$$
\geq 1 - (1 - P[f(u , i ~ | ~\overline f(u , i-1))])^i
$$
Therefore, for some $i = C \log_{4/3} n$ , $P[f(u , i)] = 1 - \dfrac{1}{n^C}$ . Therefore, by union bound, the probability that algorithm does not terminate by $i$-th iteration will be $1 - \dfrac{1}{n^{C-1}}$ . Thus, the running time of this algorithm is $O(\log n)$ with high probability.

### Tail bounds:
#### Chernoff bound:
Let $X_1 , X_2 , ... , X_k$ are independent $0/1$ random variables. Let $\mu = E[X]$ , where 
$$
E[X] = E\left[\sum_{i = 1}^n X_i \right]
$$
Then, for any $0 < \delta < 1$, 
$$
P[X > (1 + \delta) \mu] \leq e^{-\tfrac{\mu \delta^2}{3}}
$$
$$
P[X < (1 - \delta)\mu] \leq e^{-\tfrac{\mu \delta^2}{2}}
$$

#### Markov's Inequality:
Let $X$ be a random variable. Then for any $a$ , 
$$
P[X > a] \leq \dfrac{E[X]}{a}
$$

## Exercises:

Will be added soon

## Resources:

- CS6851 Distributed Algorithms Jul-Nov 25 Offering by Prof. Shreyas Pai