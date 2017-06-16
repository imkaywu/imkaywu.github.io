---
title: Union Find
tags: [Algorithm, Course notes]
categories: blog
---

## Dynamic connectivity.

The input is a sequence of pairs of integers, where each integer represents an object of some type and we are to interpret the pair `p q` as meaning `p` is connected to `q`. We assume that "is connected to" is an *equivalence relation*:

> * *symmetric*: If `p` is connected to `q`, then `q` is connected to `p`.
> * *transitive*: If `p` is connected to `q` and `q` is connected to `r`, then `p` is connected to `r`.
> * *reflexive*: `p` is connected to `p`.

An *equivalence relation* partitions the objects into *equivalence classes* or *connected components*.

## Union-Find API

The following API encapsulates the basic operations that we need.

![Union-Find API](/assets/images/uf_api.png)

## Implementation

Here are several different implementations based on using the site-indexed array `id[]` to determine whether two sites are in the same component.

### Quick-Find

Quick-Find maintains the invariant that `p` and `q` are connected if and only if `id[p]` is equal to `id[q]`. In other words, all sites in a component must have the same value in `id[]`.

![quick-find-overview](/assets/images/quick-find-overview.png)

### Quick-Union

Quick-Union is based on site-indexed `id[]` array. The `id[]` entry for each site is the name of another site in the same component(possibly itself). Two sites are in the same component if and only if they lead to the same root. 

> * find(): 
> * union():

![quick-union-overview](/assets/images/quick-union-overview.png)

### Weighted Quick-Union

Connect the smaller tree to the larger one.

![weighted-quick-union-overview](/assets/images/weighted-quick-union-overview.png)

### Weighted Quick-Union with Path Compression

Idealy, we would like every node to link directly to the root of its tree. We can approach the ideal simply by making all the nodes that we do examine directly link to the root.

## Union-Find Performance

![uf-performance](/assets/images/uf-performance.png)

[原文链接](http://algs4.cs.princeton.edu/15uf/)
