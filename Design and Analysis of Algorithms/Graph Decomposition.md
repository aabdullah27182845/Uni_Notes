
Graphs are one of the most fundamental notion in CS: Many CS problems have an underlying graph structure. 

We will begin justifying this statement with an example: Colouring a map. 

Here is the problem: What is the minimum number of colours needed so that neighbouring countries have different colours? We can change this graph filling problem with the use of graph formulation.

- One vertex is denoted for each country
- Two neighbours are linked by an edge if they border each other on the map.

The original problem can be reduced to a graph problem, known as the Graph Colouring problem: find the minimum number of colours needed to colour the vertices of the graphs so that no edge has endpoints of the same colour.

A directed graph $(V,E)$ consists of a set $V$ of nodes - or vertices - and a set $E\subseteq V\times V$ of edges, each edge $e$ being an ordered pair $(u,v)$ of nodes; u is the source of e, and v is the target of e; we say that e is incident on u and v. In this case, we also say that u and v are adjacent.

A path of length k from a vertex u to a vertex u' is a sequence $〈 v_0, v_1, · · · , v_k 〉$ of vertices such that $u = v_0$ and $u' = v_k$, and each $(v_i, v_{i+1})\in E$. If there is a path from u to u', then u' is reachable from u.

A path $〈 v_0, v_1, · · · , v_k 〉$ forms a cycle if $v_0=v_k$ and the path contains at least one edge. A self-loop is a cycle of length 1. The cycle is simple if, in addition, $v_1,...v_k$ are distinct.

An acyclic directed graph is a directed graph with no cycles. A graph $(V,E)$ is undirected if $E$ is symmetric.

A graph $G=(V,E)$ with $V=\{v_0,\cdots,v_{n-1}\}$ can be represented by the following data structures, adjacency matrix and adjacency lists.

An adjacency matrix is an $n\times n$ array whose $(i,j)$-th entry is $$a_{ij}=\begin{cases}1 \qquad \text{if} (v_i,v_j)\in E \\ 0 \qquad \text{otherwise}\end{cases}$$The presence of an edge can be checked in constant time, and the data structure has a size $O(|V|^2)$.For an undirected graph, the matrix is symmetric. Here is a visual example of an adjacency matrix:

![[Adjacency Matrix.png]]


###### Adjacency Lists

These consist of $|V|$ linked lists, one per vertex. The list for vertex $u$ holds the names of vertices to which $u$ has an outgoing edge. The presence of an edge is not checkable in constant time. The data structure has a size $O(|V|+|E|)$, and for an undirected graph, u is in v's adjacency list if and only if v is in u's.

This begs the question: Which would you use to represent the World Wide Web (as in, there is an edge between two sites if there is a link from one to the other)? We won't be answering this question, but it is a good thought experiment. Here is a visual example of adjacency lists:

![[Adjacency Lists.png]]

###### Depth-First Search

DFS is a versatile linear-time algorithm that answers the basic question: What parts of the graph are reachable from a given vertex? It works for both directed and undirected graphs.

Our motivation here would begin with solving a maze. All one needs to explore a maze are: 

1. A piece of chalk to prevent looping, and
2. A ball of string to enable return to passages encountered before but not yet explored.

We use the same basic idea in depth-first search of a graph.

Here's our idea. As soon as a new vertex is discovered, explore from it. As DFS progresses, every vertex is assigned a colour. 

- `WHITE` = not discovered yet
- `GREY` = discovered, but its adjacency list has not been fully explored yet
- `BLACK` = finished

DFS takes a graph $G = (V,E)$, directed or undirected, and, for each vertex $v\in V$, returns a back-pointer $\pi[v]$ (the predecessor of v), and two timestamps: the discovery time, and the finishing time.

Here is the pseudocode for such a DFS algorithm
- Input: Graph $G=(V,E),$ directed or undirected
- Output: Timestamps `d[v]` and `f[v]` and predecessor `pi[v]` for each $v\in V$.

```
for u in V
	colour[u] = WHITE ; pi[u] = NULL
time = 0 
for u in V
	if colour[u] = WHITR
		DFS-VISIT(u)
```

With the pseudocode of `DFS-VISIT(u)` being the following:

```
procedure DFS-Visit(u)
	time = time + 1                      // vertex u has been discovered
	d[u] = time                         // record discovery time
	colour[u] = grey                   // mark vertex u visited
	for v in Adj[u]                   // explore from v and come back once finished
		if colour[v] = white
			π[v] = u
			DFS-Visit(v)
	time = time + 1                           // vertex u has been finished
	f [u] = time                             // record finishing time
	colour[u] = black                       // mark vertex u finished
```

Remark that for all $u \in V$, one has `1 < d[u] < f[u] < 2*V.size`.

Here's a visual example of DFS running:

![[DFS.png]]
Note that $d$, $f$, and $\pi$ generally depend on the order in which the vertices of the graph are visited, and on the order of the vertices in the adjacency lists.

The loops on lines 1-2 and lines 4-6 of DFS take time $O(|V|)$, excluding the time to execute the calls to `DFS-Visit`.

Fact 1: `DFS-Visit` is called once and only once for each `v in V`, since it is invoked on white vertices, and, when it runs on a white vertex it immediately paints it grey.

Fact 2: When `DFS-Visit` runs on a vertex `v in V`, it takes time $\Theta(|\text{Adj}(v)|)$, since $\sum_{v\in V}|\text{Adj}(v)| = |E|$, this yields $T = \Theta(|V|+|E|)$.


###### The DFS Forest

Define the set of Edges $E_{\pi} := \{ (\pi[u], u) : u \in V, \pi[u]\neq \text{NIL} \}$ and the graph $G_{\pi}:=(V, E_{\pi})$.

The graph is therefore called a depth-first search forest. The DFS consists of one or more DFS trees. Each tree is composed of edges $(u,v)$ such that, when $(u,v)$ is explored, $u$ is grey and $v$ is white.

We say that u is a descendent of v just if it is in the DFS forest. The DFS forest depends on the order in which the vertices are listed.

***
Onto a **Theorem**: (Parenthesis Theorem) For all u, v, exactly one of the following holds:

1. $d[u] < f [u] < d[v] < f [v]$ or $d[v] < f [v] < d[u] < f [u]$
2. $d[u] < d[v] < f [v] < f [u]$ and v is a descendent of u.
3. $d[v] < d[u] < f [u] < f [v]$ and u is a descendent of v.

So, something like $(u, u) \; (v , v)$ and $(u \;(v,v)\;u)$ are possible, but $(u \;(v, u)\; v)$ is not possible.

***

