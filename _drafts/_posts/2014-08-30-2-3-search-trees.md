---
title: 2-3 search tree
categories: 
  - Course
  - Algorithm
tags:
  - Notes
---

## Definition

A 2-3 search tree is a tree that either is empty or:

> A 2-node, with one key(and associated value) and two links, a left link to a 2-3 search tree with smaller keys, and a right link to a 2-3 search tree with larger keys.
>
> A 3-node, with two keys(and associated values) and three links, a left link to a 2-3 search tree with smaller keys, a middle link to a 2-3 search tree with keys between the node's keys and a right link to a 2-3 search tree with larger keys.

![2-3 tree anatomy]({{ site.url }}{{ site.baseurl }}/images/2-3 tree anatomy.png)

A **perfectly balanced** 2-3 search tree is one whose null links are all the same distance from the root.

## Operations

### Search

To determine whether a key is in a 2-3 tree, we compare it against the keys at the root: If it is equal to any of them, we have a search hit; otherwise, we follow the link from the root to the subtree corresponding to the interval of key values that could contain the search key, and then recursively search in that subtree.

![2-3 tree search]({{ site.url }}{{ site.baseurl }}/images/2-3 tree search.png)

### Insert into a 2-node

To insert a new node in a 2-3 tree, we might do an unsuccessful search and then hook on the node at the bottom, as we did with BSTs, but the new tree would not remain perfectly balanced. It is easy to maintain perfect balance if the node at which the search terminates is a 2-node: We just replace the node with a 3-node containing its key and the new key to be inserted.

![2-3 tree insert 2]({{ site.url }}{{ site.baseurl }}/images/2-3 tree insert 2.png)

### Insert into a tree consisting of a single 3-node

We temporarily put the new key into a 4-node, a natural extension of our node type that has three keys and four links. Creating the 4-node is convenient because it is easy to convert it into a 2-3 tree made up of three 2-nodes, one with the middle key (at the root), one with the smallest of the three keys (pointed to by the left link of the root), and one with the largest of the three keys (pointed to by the right link of the root).

![2-3 tree insert 3a]({{ site.url }}{{ site.baseurl }}/images/2-3 tree insert 3a.png)

### Insert into a 3-node whose parent is a 2-node

We can still make room for the new key while maintaining perfect balance in the tree, by making a temporary 4-node as just described, then splitting the 4-node as just described, but then, instead of creating a new node to hold the middle key, moving the middle key to the nodes parent.

![2-3 tree insert 3b]({{ site.url }}{{ site.baseurl }}/images/2-3 tree insert 3b.png)

### Insert into a 3-node whose parent is a 3-node

We continue up the tree, splitting 4-nodes and inserting their middle keys in their parents until reaching a 2-node, which we replace with a 3-node that does not to be further split, or until reaching a 3-node at the root.

![2-3 tree insert 3c]({{ site.url }}{{ site.baseurl }}/images/2-3 tree insert 3c.png)

If we have 3-nodes along the whole path from the insertion point to the root, we end up with a temporary 4-node at the root. In this case we split the temporary 4-node into three 2-nodes.

![2-3 tree split]({{ site.url }}{{ site.baseurl }}/images/2-3 tree split.png)

## Features

### Local transformation

The basis of the 2-3 tree insertion algorithm is that all of these transformations are purely local: No part of the 2-3 tree needs to be examined or modified other than the specified nodes and links. The number of links changed for each transformation is bounded by a small constant. Each of the transformations passes up one of the keys from a 4-node to that nodes parent in the tree, and then restructures links accordingly, without touching any other part of the tree.

### Global properties

These local transformations preserve the global properties that the tree is ordered and balanced: the number of links on the path from the root to any null link is the same.

Search and insert operations in a 2-3 tree with N keys are guaranteed to visit at most lg N nodes. However, we are only part of the way to an implementation. Although it would be possible to write code that performs transformations on distinct data types representing 2- and 3-nodes, most of the tasks that we have described are inconvenient to implement in this direct representation.

[Original Course Notes](http://algs4.cs.princeton.edu/33balanced/)
