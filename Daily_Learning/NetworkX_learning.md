# Accessing edges and neighbors

In addition to the views [`Graph.edges`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.Graph.edges.html#networkx.Graph.edges), and [`Graph.adj`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.Graph.adj.html#networkx.Graph.adj), access to edges and neighbors is possible using subscript notation.

```
>>> G = nx.Graph([(1, 2, {"color": "yellow"})])
>>> G[1]  # same as G.adj[1]
AtlasView({2: {'color': 'yellow'}})
>>> G[1][2]
{'color': 'yellow'}
>>> G.edges[1, 2]
{'color': 'yellow'}
```

You can get/set the attributes of an edge using subscript notation if the edge already exists.

```
G.add_edge(1, 3)
G[1][3]['color'] = "blue"
G.edges[1, 2]['color'] = "red"
G.edges[1, 2]
# G.edges[1, 2] = G[1][3]
```



## Adding attributes to graphs, nodes, and edges

## Graph attributes

Assign graph attributes when creating a new graph

```
G = nx.Graph(day="Friday")
G.graph
G.graph['day'] = "Monday"
```

## Node attributes

Add node attributes using `add_node()`, `add_nodes_from()`, or `G.nodes`

```
G = nx.Graph()
G.add_node(1, time='5pm')
G.add_nodes_from([3], time='2pm')
print(G.nodes[1])
G.nodes[1]['room'] = 714
print(G.nodes.data())
```

Note that adding a node to `G.nodes` does not add it to the graph, use `G.add_node()` to add new nodes. Similarly for edges.

## Edge Attributes

Add/change edge attributes using `add_edge()`, `add_edges_from()`, or subscript notation.

```
G = nx.Graph()
G.add_edge(1, 2, weight=4.7)
G.add_edges_from([(3, 4), (4, 5)], color = 'red')
G.add_edges_from([(1, 2, {'color': 'blue'}), (2, 3, {'weight': 8})])
print(G.edges())
G[1][2]['weight'] = 4.2
G.edges[3, 4]['weight'] = 4.21
print(G.edges[1, 2])
```

# Directed graphs

The [`DiGraph`](https://networkx.org/documentation/stable/reference/classes/digraph.html#networkx.DiGraph) class provides additional methods and properties specific to directed edges, e.g., [`DiGraph.out_edges`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.DiGraph.out_edges.html#networkx.DiGraph.out_edges), [`DiGraph.in_degree`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.DiGraph.in_degree.html#networkx.DiGraph.in_degree), [`DiGraph.predecessors`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.DiGraph.predecessors.html#networkx.DiGraph.predecessors), [`DiGraph.successors`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.DiGraph.successors.html#networkx.DiGraph.successors) etc. To allow algorithms to work with both classes easily, the directed versions of [`neighbors`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.DiGraph.neighbors.html#networkx.DiGraph.neighbors) is equivalent to [`successors`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.DiGraph.successors.html#networkx.DiGraph.successors) while [`degree`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.DiGraph.degree.html#networkx.DiGraph.degree) reports the sum of [`in_degree`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.DiGraph.in_degree.html#networkx.DiGraph.in_degree) and [`out_degree`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.DiGraph.out_degree.html#networkx.DiGraph.out_degree) even though that may feel inconsistent at times.

```
G = nx.DiGraph()
G.add_weighted_edges_from([(1, 2, 0.5), (3, 1, 0.75)])
print(G.out_degree(1, weight='weight'))
print(G.degree(1, weight='weight'))
print(list(G.successors(1)))
print(list(G.neighbors(1)))
```

## Digraph

a DiGraph stores nodes and edges with optional data, or attributes.

Digraphs hold directed edges. Self loops are allowed but multiple (parallel) edges are not. (directed edges 有向边)

Nodes can be arbitrary (hashable) Python objects with optional key/value attributes. By convention [`None`](https://docs.python.org/3/library/constants.html#None) is not used as a node.

### Examples

```
G = nx.Digraph()
```

#### Nodes:

```
G.add_node(1)
```

Add the nodes from any container (a list, dict, set or even the lines from a file or the nodes from another graph).

```
G.add_nodes_from([2, 3])
G.add_nodes_from(range(100, 110)) # [100, 101, 102, 103, 104, 105, 106, 107, 108, 109]
H = nx.path_graph(10)
G.add_nodes_from(H)
```

In addition to strings and integers any hashable Python object (except None) can represent a node, e.g. a customized node object, or even another Graph.

```
G.add_node(H)
```

#### Edges:

```
G.add_edge(1, 2)
G.add_edges_from([(1, 2), (1, 3)])
G.add_edges_from(H.edges)
```

#### Attributes:

Each graph, node, and edge can hold key/value attribute pairs in an associated attribute dictionary (the keys must be hashable). By default these are empty, but can be added or changed using add_edge, add_node or direct manipulation of the attribute dictionaries named graph, node and edge respectively.

```
G = nx.DiGraph(day="Friday")
```

Add node attributes using add_node(), add_nodes_from() or G.nodes

Add edge attributes using add_edge(), add_edges_from(), subscript notation, or G.edges.

#### Shortcuts

Many common graph features allow python syntax to speed reporting.

```
1 in G #check if node in graph
[n for n in G if n < 3] # iterate through nodes
len(G) # number of nodes in graph
# G.adj = G.adjacency()
```

# Multigraphs

NetworkX provides classes for graphs which allow multiple edges between any pair of nodes. The [`MultiGraph`](https://networkx.org/documentation/stable/reference/classes/multigraph.html#networkx.MultiGraph) and [`MultiDiGraph`](https://networkx.org/documentation/stable/reference/classes/multidigraph.html#networkx.MultiDiGraph) classes allow you to add the same edge twice, possibly with different edge data. This can be powerful for some applications, but many algorithms are not well defined on such graphs. Where results are well defined, e.g., [`MultiGraph.degree()`](https://networkx.org/documentation/stable/reference/classes/generated/networkx.MultiGraph.degree.html#networkx.MultiGraph.degree) we provide the function. Otherwise you should convert to a standard graph in a way that makes the measurement well defined.

```
MG = nx.MultiGraph()
MG.add_weighted_edges_from([(1, 2, 0.5), (1, 2, 0.75), (2, 3, 0.5)])
print(dict(MG.degree(weight='weight')))
GG = nx.Graph()
for n, nbrs in MG.adjacency():
    for nbr, edict in nbrs.items():
        minvalue = min([d['weight'] for d in edict.value()])
        GG.add_edge(n, nbr, weight = minvalue)
nx.shortest_paths(GG, 1, 3)
```

# Graph generators and graph operations

# Analyzing graphs

```
G = nx.Graph()
G.add_edges_from([(1,2), (1,3)])
G.add_node("spam")
print(list(nx.connected_components(G)))
# sorted(d for n, d in G.degree())
print(sorted(d for n, d in G.degree()))
print(nx.clustering(G))
sp = dict(nx.all_pairs_shortest_path(G))
print(sp[3])
```

[Algorithms](https://networkx.org/documentation/stable/reference/algorithms/index.html)

# Drawing graphs

NetworkX is not primarily a graph drawing package but basic drawing with Matplotlib as well as an interface to use the open source Graphviz software package are included. These are part of the [networkx.drawing](https://networkx.org/documentation/stable/reference/drawing.html) module and will be imported if possible.

```
G = nx.petersen_graph()
subax1 = plt.subplot(121)
nx.draw(G, with_labels=True, font_weight='bold')
subax2 = plt.subplot(122)
nx.draw_shell(G, nlist=[range(5, 10), range(5)], with_labels=True, font_weight='bold')
plt.show()
```

