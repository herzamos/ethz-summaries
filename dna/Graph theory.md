[TOC]

# Graph theory

## Glossary

- **Graph G  (V, E)**:
  - **V**: vertices set
  - **E**: edges set
- **Degree**: number of vertices
- **Walk**: series of connected vertices
- **Path**: walk without repeated vertices
- **Closed walk**: walk where v~0~ = v~n~
- **Cycle**: closed walk without repeating vertices
- **Euler path**: visit each edge exactly once
- **Hamilton path**: visit each vertex exactly once
- **Directed graph**: edges are ordered pairs
- **Ancestor**: v, **Successor**: u  in 

```mermaid
graph LR
	A((v)) --> B((u))
```

- **deg~in~(v)**: number of incoming edges into v

- **deg~out~(v)**: number of outgoing edges into v

  

## Graph Representation

### Adjacency matrix:

matrix where $A_{uv} = \begin{cases} 1 & \text{if} (u,v) \in E \\ 0 & \text{otherwise} \end{cases}$
**Graph:**

```mermaid
graph LR 
	A((1)) & B((2)) --- D((4))
	A --- C((3))
	C --- D
	A --- B

```

**Matrix:**
$$
\begin{pmatrix}
0 & 1 & 1 & 1\\
1 & 0 & 0 & 1\\
1 & 0 & 0 & 1\\
1 & 1 & 1 & 0\\
\end{pmatrix}
$$

  ### Adjacency list

Array of linked lists, where Adj[u] contains a list containing all the neighbors of u.
**Graph:** Same as above
**List:**

```mermaid
graph TB
1 & 2 & 3 & 4
1 --> A[2] --> B[3]
2 --> C[1] --> D[3] --> E[4]
3 --> AA[1] --> BB[2] --> CC[4]
4 --> AAA[2] --> BBB[3]
```

### Runtimes

|                                              | Matrix                   | List                                               |
| -------------------------------------------- | ------------------------ | -------------------------------------------------- |
| Find all neighbors of $v$                    | $\mathcal{O}(n)$         | $\mathcal{O}(deg_{out}(v))$                        |
| Find $v \in V$ without neighbors             | $\mathcal{O}(n^2)$       | $\mathcal{O}(n)$                                   |
| Check if $(v,u) \in E$                       | $\mathcal{O}(1)$         | $\mathcal{O}(1 + min(deg_{out}(v), deg_{out}(u)))$ |
| Insert edge                                  | $\mathcal{O}(1)$         | $\mathcal{O}(1)$                                   |
| Remove edge $v$                              | $\mathcal{O}(1)$         | $\mathcal{O}(deg_{out}(v))$                        |
| Check whether an Eulerian path exists or not | $\mathcal{O}(|V| * |E|)$ | $\mathcal{O}(|V| + |E|)$                           |

 ## Algorithms

### Depth-First Search (DFS)

Used mainly to check whether a Graph can be topological sorted or not ($\Leftrightarrow$ has a cycle). A **topological sorting** of a graph it's a sequence of all its nodes with the property that a node $u$ comes after a node $v$ **if and only if** either a walk from $v$ to $u$ exists or $u$ cannot be reached starting from $v$.

#### Pseudocode

```pseudocode
DFS(G):
	for (v in V not marked):
		DFS-Visit(v)
```

```pseudocode
DFS-Visit(v):
	t = 0
	pre[v] = t++
	marked[v] = true
	for ((u, v) in E not marked)
		DFS_Visit(u)
	post[u] = t++
```

#### Runtime

| Operations | $T(n) \in \Theta(|E| + |V|)$ |
| ---------- | ---------------------------- |
| **Memory** | $T(n) \in \Theta(|V|)$       |

#### Edge classification (post and pre numbers)

**Example:** `DFS(A)` got called

```mermaid
graph LR
	A((1/16 <br> A))
	B((2/11 <br> B))
	C((12/15 <br> C))
	A --> B
	A --> C
	A --> F((4/7 <br> F))
	B --> E((3/10 <br> E))
	C --> D((13/14 <br> D))
	D --> A
	D -->H
	E --> F
	E --> G((5/6 <br> G))
	E --> H((8/9 <br> H))
	F --> B
	F --> G
	H --> G
```

This graph generate the following tree (rotated of 90 degree to save space):

```mermaid
graph LR
	A((A)) --> B((B)) & C((C))
	B --> E((E))
	C --> D((D))
	E --> F((F)) & H((H))
	F --> G((G))
	D --> |back| A
	F ----> |back| B
	A ----> |forward| F
	D ---> |cross| H
	H ---> |cross| G
	E --> |forward| G
```

| Pre and post number                                          | Name of the edge $(v, u) \in E$                             |
| ------------------------------------------------------------ | ----------------------------------------------------------- |
| $pre(u) < pre(v)$ and $post(u) < post(v)$                    | Not possible                                                |
| $pre(u) < pre(v)$ and $post(u) > post(v)$                    | **forward** or simply no name, since it is part of the tree |
| $pre(u) < pre(w)$ and $post(u) < post(v)$ but $(u,v) \notin E$ | **forward edge**                                            |
| $pre(u) > pre(v)$ and $post(u) > post(v)$                    | **back edge**                                               |
| $pre(u) > pre(v)$ and $post(u) > post(v)$                    | **cross edge**                                              |
| $pre(u) < pre(v)$ and $post(u) < post(v)$                    | Not possible                                                |

**Remark:** $\nexists$ back edge $\Leftrightarrow$ $\nexists$ closed walk (cycle)

### Breadth-First Search (BFS)

Instead of searching through the depth of a graph, one can also go first through all the successor of the root with the BFS algorithm.

#### Pseudocode

```pseudocode
BFS(G):
	for (v in V not marked):
		DFS-Visit(v)
```



```pseudocode
DFS-VIsit(v):
	Q = new Queue()
	active[v] = true //used to check whether a vertex is in the queue or not
	enqueue(v, Q)
	while (!isEmpty(Q)):
		w = dequeue(Q)
		visited[W] = true
		for ((w, x) in E):
			if(!active[x] && !visited[x]):
				active[x] = true
				enqueue(x, Q)
```

#### Runtime

| Operations | $T(n) \in \Theta(|E| + |V|)$ |
| ---------- | ---------------------------- |
| **Memory** | $T(n) \in \Theta(|V|)$       |

### Find shortest path in DAG (Directed Acyclic Graph) 

We can compute a recurrence following the topological sorting of the graph.

#### Pseudocode

```pseudocode
ShortestPath(V):
	d[s] = 0, d[v] = inf
	for (v in V \ {s}, following topological sorting):
		for (u, v, s.t. (u, v) in E):
			d[v] = min(d[u] + c(u,v)) 
```

#### Runtime 

$T(n) \in \mathcal{O}(|E| * |V|)$ if adjacency list is given

### Djikstra 

Used to find the shortest (cheapest) path between two nodes in a graph. 
**Remark:** The graph must **not** have negative weights

#### Pseudocode

```pseudocode
DijkstraG, s):
	for (v in V):
		distance[v] = infinity
		parent[v] = null
	distance[s] = 0
	Q = new Queue()
	insert(s, 0, Q) // insert s into the queue Q, with priority 0 (min)
	while(!isEmpty(Q)):
		v* = Q.extractMin() // extract from Q the node with minimum distance
		for ((v*, v) in E):
			if (parent[v] == null):
				distance[v] = distance[v*] + w(v*, v)
				parent[v] = v*
			else if (distance[v*] + w(v*, v) < distance[v]):
				distance[v] = distance[v*] + w(v*, v)
				parent[v] = v*
				decreaseKey(v, distance[v], Q)
```

#### Runtime

$T(n) \in \mathcal{O}((|E| * |V|)*log(|V|))$ if implemented with a Heap.
If implemented with a **Fibonacci-Heap**: $T(n) \in \mathcal{O}((|E| + |V|*log(|V|))$)

### Bellman-Ford

Used for graph with general weight (**positive and negative!**)

#### Pseudocode

```pseudocode
BellmanFord(G, s):
	for (v in V):
		distance[v] = infinity
		parent[v] = null
	distance[s] = 0
	for (i = 1, 2, ..., |V| - 1):
		for ((u, v) in E):
			if(distance[v] > distance[u] + w(u, v)):
				distance[v] = distance[u] + w(u, v)
				parent[v] = u
	for ((u, v) in E):
		if (distance[u] + w(u, v) < distance [v]):
			return "negative cyrcle!"
```

#### Runtime

$T(n) \in \mathcal{O}(|E| * |V|)$