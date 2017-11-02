---
title: Red-black BSTs
categories: 
  - Course
  - Algorithm
tags:
  - Notes
---

The *Red-black BST* guarantees the costs to be logarithmic, and is near-perfect balanced, where the height is guaranteed to be no longer than `2lgN`.

The red-black BST is a natural implementation of [2-3 tree](/2014/08/30/2-3-search-trees.html)

## Implementation

### Encoding 3-nodes

The basic idea behind red-black BSTs is to encode 2-3 trees by starting with standard BSTs (which are made up of 2-nodes) and add extra information to encode 3-nodes. The links have 2 different types: red links, which bind together 2-nodes to represent 3-nodes, and black links, which bind together the 2-3 tree. Specifically, we represent 3-nodes as two 2-nodes connected by a single red link that leans left. This is refered to as left-leaning red-black tree BSTs.

![red-black encoding]({{ site.url }}{{ site.baseurl }}/images/red-black encoding.png)

### A 1-1 correspondence

![1-1 correspondence between red-black BSTs and 2-3 trees]({{ site.url }}{{ site.baseurl }}/images/red-black and 2-3.png)

### Color representation

Since each node is pointed to by precisely one link (from its parent), we encode the color of links in nodes, by adding a boolean instance variable color to our Node data type, which is true if the link from the parent is red and false if it is black. By convension, null links are black.

![red-black color]({{ site.url }}{{ site.baseurl }}/images/red-black color.png)

### Rotation

The operation called rotation corrects two situations: right-leaning links and two red-links in a row. For the first situation, a left-rotation converts a right-leaning red link to a left-leaning link. For the second one, a right-rotation convers a 2-red-links-in-a-row situation into a 3 2-nodes BST with no red links.

![red-black left rotation]({{ site.url }}{{ site.baseurl }}/images/red-black left rotate.png)

![red-black right rotation]({{ site.url }}{{ site.baseurl }}/images/red-black right rotate.png)

### Flipping colors

The black parent has two red children can use the operation *flip* to flip the colors of the two red children to black and the color of the black parent to be red.

![red-black color flip]({{ site.url }}{{ site.baseurl }}/images/red-black color-flip.png)

### Insert into a single 2-node

### Insert into a 2-node at the bottom

### Insert into a tree with two keys (in a 3-node)

### Keeping the root black

### Insert into a 3-node at the bottom

### Passing a red link up the tree

[Original Course Notes](http://algs4.cs.princeton.edu/33balanced/)
