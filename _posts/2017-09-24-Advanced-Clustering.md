---
title: 'Data Mining - Advanced Concepts and Algorithms of Cluster Analysis'
layout: post
tags:
  - Clustering
  - Data-Mining
category: Notes
mathjax: true
---

Agglomerative clustering algorithms vary in terms of how the proximity of two clusters are computed. However, with MIN (single link), it is susceptible to noise/outliers; with MAX/GROUP AVERAGE, it may not work well with non-globular clusters.

So how can we deal with these two types of problem?

<!--more-->

## Clustering Using REpresentatives (CURE)

CURE (Clustering Using REpresentatives) is an efficient data clustering algorithm for large databases that is more robust to outliers and identifies clusters having non-spherical shapes and size variances.

CURE employs a hierarchical clustering algorithm that adopts a middle ground between the centroid based and all point extremes.


- Strengths
    - Shrinking representative points toward the center helps avoid problems with noise and outliers
    - Handle clusters with varying sizes.
- Limitations
    - Cannot handle different densities

### Representative Points

In CURE, a constant number $c$ of well scattered points of a cluster are chosen and they are shrunk towards the centroid of the cluster by a fraction $α$.   The scattered points after shrinking are used as representatives of the cluster.

### Cluster Similarity

Cluster similarity is the similarity of the closest pair of representative points from different clusters.   That is, the clusters with the closest pair of representatives are merged at each step of CURE's hierarchical clustering algorithm.

## Graph-Based Clustering

Graph-Based clustering uses the proximity graph such that:

- Consider each point as a node in a graph
- Each edge between two nodes has a weight which is the proximity between the two points
- Initially the proximity graph is fully connected 

In graph-based clustering, clusters are **connected components** in the graph.

For example, Minimal Spanning Tree (MST), which can be built by Prim's or Kruskal's algorithm, is one way for performing graph-based clustering.

### Sparsification Techniques

Sparsification can eliminate more than 99% of the entries in a proximity matrix.   The amount of time required to cluster the data can be drastically reduced.

Clustering may work better since sparsification techniques keep the connections to the most similar (nearest) neighbors of a point while breaking the connections to less similar points. This reduces the impact of noise and outliers and sharpens the distinction between clusters.

In general, Sparsification facilitates the use of graph partitioning algorithms.

![](https://i.imgur.com/vQaPOwj.png)

### CHAMELEON: Clustering Using Dynamic Modeling

Limitations of current merging schemes are shown as belows:

| Closeness schemes | Average connectivity schemes |
| - | - |
| e.g., MIN, CURE | e.g., MAX, GROUP AVERAGE |
| ![](https://i.imgur.com/5GhLJuP.png) | ![](https://i.imgur.com/JrK6f7p.png) |
| Closeness schemes will merge (a) and (b) | Average connectivity schemes will merge (c) and (d)|

This is the reason why we need to adapt to the characteristics of the data set to find the natural clusters. That is, we use a dynamic model to measure the similarity between clusters

CHAMELEON uses a dynamic model to measure the similarity between clusters.   The main property of this dynamic model is the relative closeness and relative inter-connectivity of the cluster.

> **Relative Interconnectivity** is the absolute interconnectivity of two clusters normalized by the *internal connectivity* of the clusters

> **Relative Closeness** is the absolute closeness of two clusters normalized by the *internal closeness* of the clusters

Two clusters are combined if the resulting cluster shares certain properties with the constituent clusters, which means, the merging scheme preserves self-similarity.


![](https://i.imgur.com/JcFYiUg.jpg)


**Preprocessing Step**

- Given a set of points, construct the **k-nearest-neighbor (k-NN) graph** to capture the relationship between a point and its k nearest neighbors.
- Concept of neighborhood is captured dynamically (even if region is sparse).

**Phase 1**

Use a **multilevel graph partitioning algorithm** on the graph to find a large number of clusters of **well-connected** vertices.   That is, Each cluster should contain mostly points from one “true” cluster.

**Phase 2**

Use **Hierarchical Agglomerative Clustering** to merge sub-clusters.   Two clusters are combined if the resulting cluster shares certain properties (e.g., Relative Interconnectivity, Relative Closeness) with the constituent clusters.

## Shared Near Neighbor Approach (SNN)

In a Sparse Graph, link weights are similarities between neighboring points.

In a SNN graph, the weight of an edge is **the number of shared nearest neighbors**, that is, the number of shared neighbors between vertices given that the vertices are connected.

![](https://i.imgur.com/Vsmurrf.png)

However, the limitations of SNN Clustering is that it does not cluster all points and the complexity of its algorithms is high.


### RObust Clustering using linKs (ROCK)

1. Obtain a sample of points from the data set
2. **Compute the link value** for each set of points
    - i.e., transform the original similarities (computed by *Jaccard coefficient*) into **similarities that reflect the number of shared neighbors between points**
3. Perform an **agglomerative hierarchical clustering** on the data using the “number of shared neighbors” as similarity measure
    - **maximizing “the shared neighbors” objective function**
4. Assign the remaining points to the clusters that have been found

### Jarvis-Patrick Clustering

Treat each point as a cluster first.

Given parameter $T$ and $k$, the procedure of Jarvis-Patrick Clustering is defined as below.

1. Find the $k$-nearest neighbors of all points.
    - In graph terms this can be regarded as breaking all but the $k$ strongest links from a point to other points in the proximity graph
2. A pair of points is put in the same cluster if they share more than $T$ neighbors. (These shared neighbors should be in each other's $k$-nearest neighbor list).
3. Repeat step 2 until there is only a small number of cluster (e.g., 1 cluster).

### SNN Clustering Algorithm

1. Compute the similarity matrix
2. Sparsify the similarity matrix by keeping only the k most similar neighbors
    - This corresponds to only keeping the k strongest links of the similarity graph
3. Construct the shared nearest neighbor graph from the sparsified similarity matrix.
    - At this point, we could apply a similarity threshold and find the connected components to obtain the clusters
    - Jarvis-Patrick algorithm
4. Perform DBSCAN on these points.
    - Find the SNN density of each Point.
        - SNN density of a point is the number of points that have an SNN similarity of $Eps$ or greater to each point.
    - Find the core points
        - Find all points that have an SNN density greater than $MinPts$
    - Form clusters from the core points
    - Discard all noise points
    - Assign all non-noise, non-core points to clusters
        - This can be done by assigning such points to the nearest core point


## References
- [“Introduction to Data Mining,” by P.-N. Tan, M. Steinbach, V. Kumar, Addison-Wesley.](http://www-users.cs.umn.edu/~kumar/dmbook/index.php)
- [Wikipedia - CURE data clustering algorithm](https://en.wikipedia.org/wiki/CURE_data_clustering_algorithm)
- [Chameleon Clustering](http://mlwiki.org/index.php/Chameleon_Clustering)
- [Data Mining Algorithms In R/Clustering/RockCluster](https://en.wikibooks.org/wiki/Data_Mining_Algorithms_In_R/Clustering/RockCluster)