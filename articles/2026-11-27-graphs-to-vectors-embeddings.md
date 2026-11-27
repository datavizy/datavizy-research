# From Graphs to Vectors: What Embeddings Keep, and What They Leave Behind

A recommendation model is ready for its input table, but the data arrive as a network: customers connect to products, products connect to categories, and a new customer signs up every day. A tempting solution is to turn every possible connection into a feature. That works on paper, until the table becomes enormous or a new node changes its shape. Graph embeddings offer another route: represent graph objects with compact vectors, while trying to preserve the parts of the graph that matter for a task.

This chapter introduces the ideas behind those vectors and two different strategies for making them: Node2Vec and GraphSAGE. The key questions are not just how to produce an embedding, but what information it captures, whether it can handle new nodes, and how to check that the resulting representation is useful.

## Why not feed the adjacency matrix to the model?

For a graph with $N$ nodes, an adjacency matrix $A$ records which node pairs are connected. In a weighted graph, $A_{ij}$ is the weight of the edge from node $i$ to node $j$. An unweighted matrix typically uses 1 for an edge and 0 for no edge. The matrix has $N$ rows and $N$ columns, so it has $N^2$ entries, even when most possible edges do not exist.

Here is a small undirected graph with nodes A, B, C, D, and E, and edges A–B, A–C, B–C, C–D, and D–E. In the order A, B, C, D, E, its adjacency matrix is:

$$
A =
\begin{bmatrix}
0&1&1&0&0\\
1&0&1&0&0\\
1&1&0&1&0\\
0&0&1&0&1\\
0&0&0&1&0
\end{bmatrix}
$$

The matrix has $5^2=25$ entries, but only five undirected edges. A sparse representation can avoid storing all those zeros, which is useful for some computations. It does not, however, solve every problem. A model trained on columns representing the five nodes expects that same feature layout later. Add node F, connected to E, and the matrix becomes $6\times6$. The model's input now needs six columns rather than five. The new node is not simply another row: the definition of the feature space has changed too.

This is the central motivation for graph embeddings. Instead of using one feature for every possible node, assign each node a vector with a chosen, much smaller number of dimensions. For example, a 64-dimensional embedding has 64 values per node, whether the graph has hundreds or millions of nodes. The compactness is useful, but it comes with a trade-off: a vector cannot preserve every detail of the original graph.

## An embedding is a task-shaped summary

A graph embedding maps some graph object to a vector. The object might be a node, a whole graph, or an edge. Node embeddings are the focus here: each node becomes an observation represented by a row of a feature matrix. This makes the representation compatible with many conventional machine-learning methods.

“Preserve the graph” is not a precise objective by itself. A useful representation depends on what the model needs to do. Imagine a network of clinics and referrals. For predicting which clinic might refer a patient to another, proximity in the referral network might matter. For detecting clinics with similar referral patterns, similarity of local neighborhoods might matter more, even if the clinics are far apart in the network.

The distinction is often described as positional versus structural information. A positional embedding tends to represent graph proximity: nodes close together in the graph may be close in vector space. A structural embedding aims to represent neighborhood patterns: distant nodes with similar local connections may receive similar vectors. These are tendencies, not universal guarantees. An embedding method's objective and training data determine what it learns, and downstream evaluation should test whether that information serves the intended task.

Dimension is another choice with consequences. More dimensions allow a representation to retain more distinctions, but increase storage and computation. Fewer dimensions make the feature table smaller, but may discard information needed for the task. There is no single best dimension independent of the graph, algorithm, and application.

## Worked example: walk contexts in a tiny network

Return to the five-node graph above. Suppose we want to describe the context of node C using short paths. One possible random walk starting at C is C → A → B → C. A window of one step around each position in this walk yields nearby node pairs such as (C, A), (A, B), and (B, C). A collection of walks can supply many such pairs.

For a simple count, the walk contains four node positions and three consecutive transitions: C to A, A to B, and B to C. If we count each undirected transition once in this walk, the observed transitions are exactly those three. This is not yet an embedding. It is a small sample of the kind of context that a walk-based method can use to train one. Different walks, starting nodes, window sizes, and training choices can change the contexts and learned vectors.

The example also shows why graph context is not the same as a node's label. Node C is directly connected to A, B, and D, while the walk happened to visit only A and B after starting there. A finite sample can miss real connections. A learned vector summarizes the training signal; it is not a complete inventory of edges.

As a separate illustration of compression, suppose we describe each node using two hand-designed values: its degree and the degree of its highest-degree neighbor. In this graph the degrees are A: 2, B: 2, C: 3, D: 2, and E: 1. Node C has neighbors A, B, and D, each of degree 2, so its two-value description is (3, 2). Node D has neighbors C and E, whose degrees are 3 and 1, so its description is (2, 3). These are explicit, interpretable graph features, not learned embeddings. They illustrate the general idea of replacing a large graph description with a small vector, while reminding us that the choice of what to retain matters.

## Node2Vec: learn from walks through the known graph

Node2Vec builds on the idea of representing sequences of nodes like word sequences in text. A text method can learn from words that appear near one another. For a graph, random walks create node sequences, and nearby nodes in those sequences provide training contexts. A skip-gram-style objective learns vectors that help predict which nodes occur in a context around a given node.

Walk length controls how many steps a generated sequence can take. The number of walks per node affects how much sampled context is collected and generally increases work as it grows. A context window determines which positions in a walk count as neighbors for training. The embedding dimension sets the vector length.

Node2Vec adds two controls over the next step in a walk. The return parameter $p$ influences the chance of stepping back to the node just visited. The in-out parameter $q$ influences the preference for exploring nodes near the previous node versus moving outward. Their effects are mediated through transition probabilities; they are not direct instructions to produce a particular scientific result. Changing them changes the sampled contexts, and therefore can change the representation.

In the chapter's classification, Node2Vec is transductive: it learns representations for the graph supplied during training. When new nodes arrive, it cannot simply apply a learned rule to embed them as unseen observations. The graph or embedding process needs to be recomputed to incorporate them. This can be perfectly practical when the graph is relatively stable and the application can use a fixed snapshot. It is a poor fit when fresh nodes must receive representations immediately without retraining.

## GraphSAGE: learn a rule for producing representations

GraphSAGE is presented as an inductive alternative. Rather than relying only on a fixed vector assigned to each known node, it learns to build a representation from node features and information aggregated from neighborhoods. If a new node has the inputs and neighborhood information expected by the trained model, the learned procedure can produce an embedding for it.

That ability is valuable for growing graphs, but it does not mean every new node can be embedded under all conditions. The node's available features, graph connections, and compatibility with the training setup matter. If the model depends on node properties, those properties need to be available and represented consistently. The chapter distinguishes GraphSAGE from Node2Vec in part because GraphSAGE can use node properties alongside graph structure, while Node2Vec, as described there, does not.

Inductive does not mean automatically better. If the whole graph is known and stable, a transductive approach may suit the workflow. If the graph changes continually, an inductive method may better match the deployment need. The choice depends on the arrival of new data, available features, computational constraints, and the information the task should preserve.

## Build a reproducible embedding workflow

Treat the embedding as a modeling decision, not as a neutral preprocessing step. Record the graph snapshot or data version, node and relationship definitions, selected algorithm, important parameters, and embedding dimension. Keep training and evaluation boundaries clear. For example, if the goal is to predict future links, do not let future links shape the representation used to evaluate those predictions. Otherwise, the test can quietly benefit from information it was meant to predict.

Then evaluate the representation against the actual task. A visualization that places nodes into two dimensions can help exploration, but visible clusters do not establish predictive value. Compare downstream performance with appropriate baselines, inspect errors, and check whether results remain useful under relevant graph changes. Embedding coordinates themselves may be hard to interpret: two dimensions can rotate or otherwise change across training runs without changing the meaningful relationships represented by the vectors.

The Neo4j Graph Data Science setting described in the selected chapter covers Node2Vec and GraphSAGE, including training, computing representations, and storing trained models in a model catalog for reuse. Such a catalog can help make a workflow repeatable, but it does not replace recording the graph version and the assumptions under which a model was trained. Model reuse is useful only when the new data and intended use are compatible with the stored model.

## Common pitfalls to watch for

- **Assuming every embedding preserves the same thing.** Walk-based proximity and neighborhood-pattern similarity are different signals. Choose and evaluate against the task.
- **Treating a compact vector as lossless.** Dimensionality reduction necessarily trades detail for size. A chosen representation may omit information important to another task.
- **Confusing transductive and inductive behavior.** A representation learned for known nodes is not automatically a procedure for unseen nodes.
- **Ignoring graph and feature changes.** New edges, changed node properties, or altered sampling can shift what the model learns.
- **Reading coordinates as explanations.** Vector values are learned features, not necessarily human-readable causes. Correlation with an outcome does not establish causation.

## Exercises with short answers

1. A graph has 2,000 nodes. How many entries are in its dense adjacency matrix? **Answer:** $2{,}000^2=4{,}000{,}000$.
2. A new node arrives, and the application must embed it without retraining on the entire graph. Which of the two chapter algorithms better matches this requirement? **Answer:** GraphSAGE, because it is described as inductive, subject to compatible inputs and neighborhoods.
3. Two distant nodes have very similar neighbors. Which embedding objective might bring them together? **Answer:** A structural one, because it emphasizes similar neighborhood patterns rather than graph proximity.

Graph embeddings make graph data easier to use in familiar machine-learning pipelines, but they do so by deciding what information to compress and how. Start with the task, choose a method whose behavior fits the data lifecycle, and test the resulting features rather than assuming that a small vector has captured everything important.

---

Published 2026-11-27.

**Source:** Graph Data Science with Neo4j _ Learn How to Use Neo4j 5 -- Scifo, Estelle -- 1st edition, Birmingham, UK, 2023, section **7 Automatically Extracting Features with Graph Embeddings for Machine Learning**.

This is an original explanatory note; the source book is not redistributed.
