---
title: Binary Search Trees
categories: 
  - Dev
  - Course
tags:
  - Algorithm
  - Notes
---

## Definition

A *binary search tree (BST)* is a binary tree where each node has a key and an associated value, and satisfies the restriction that the key in any node is larger than the keys in all nodes in that node's left subtree and smaller than the keys in all nodes in that node's right subtree.

![binary tree anatomy]({{ site.url }}{{ site.baseurl }}/images/binary tree anatomy.png)

![binary search tree anatomy]({{ site.url }}{{ site.baseurl }}/images/bst anatomy.png)

## Implementation

Implement the ordered symbol table using binary search tree.

### Node

Each node contains a key, a value, a left link and a right link. The left link points to a BST for items with smaller keys, and the right link points to a BST for items with larger keys. The instance variable `N` gives the node count in the subtree rooted at the node.

![bst subtree count]({{ site.url }}{{ site.baseurl }}/images/bst subtree count.png)

### Search

If the tree is empty, we have a search miss; if the search key is equal to the key at the root, we have a search hit. Otherwise, we search (recursively) in the appropriate subtree.

![bst search]({{ site.url }}{{ site.baseurl }}/images/bst search.png)

```java
public Value get(Key key) {
    while(root != null) {
        int cmp = key.compareTo(root.key);
        if(cmp < 0)
            root = root.left;
        else if(cmp > 0)
            root = root.right;
        else
            return root.value;
    }
    return null;
}
```

### Insert

If the tree is empty, we return a new node containing the key and value; if the search key is less than the key at the root, we set the left link to the result of inserting the key into the left subtree; otherwise, we set the right link to the result of inserting the key into the right subtree.

![bst insert]({{ site.url }}{{ site.baseurl }}/images/bst insert.png)

```java
public void put(Key key, Value val) {
    if(val == null) {
        delete(key);
        return;
    }
    root = put(root, key, val);
}

private Node put(Node root, Key key, Value val) {
    if(root == null)
        return new Node(key, val, 1);
    int cmp = key.compareTo(root.key);
    if(cmp < 0）
        root.left = put(root.left, key, val);
    else if(cmp > 0)
        root.right = put(root.right, key, val);
    else
        root.val = val;

    root.N = 1 + size(x.left) + size(x.right);
    return root;
}
```

## Order-based methods and deletion

Implement the numerous methods in the ordered symbol-table API.

### Minimum and maximum.

The smallest key in the BST is the smallest key in the subtree rooted at the node referenced by the left link. Find the maximum is similar, just move to the right.

```java
public Key min() {
    if(isEmpty())
        return null;

    Node x = root;
    while(x.left != null) {
        x = x.left;
    }
    return x.key;
}

public Key max() {
    if(isEmpty())
        return null;

    Node x = root;
    while(x.right != null) {
        x = x.right;
    }
}
```

### Floor and ceiling

If a given `key` is less than the key at the root of a BST, then the floor of `key` (the largest key in the BST less than or equal to `key`) **must be** in the left subtree. If `key` is greater than the key at the root, then the floor of `key` **could be** in the right subtree, but only if there is a key smaller than or equal to `key` in the right subtree; if not (or if `key` is equal to the key at the root), then the key at the root is the floor of `key`. Finding the ceiling is similar, interchange right and left. 

![bst floor]({{ site.url }}{{ site.baseurl }}/images/bst floor.png)

```java
public Key floor(Key key) {
    Node x = floor(root, key);
    if(x == null)
        return null;
    else
        return x.key;
}

private Node floor(Node x, Key key) {
    if(x == null)
        return null;

    int cmp = key.compareTo(x.key);

    if(cmp ==0)
        return x;

    if(cmp < 0）
        return floor(x.left, key);

    Node t = floor(x.right, key);
    if(t != null)
        return t;
    else
        return x;
}

public Key ceiling(Key key) {
    Node x = ceiling(root, key);
    if(x == null)
        return null;
    else
        return x.key;
}

private Node ceiling(Node x, Key key) {
    if(x == null)
        return null;

    int cmp = key.compareTo(x.key);

    if(cmp == 0)
        return x;

    if(cmp < 0) {
        Node t = ceiling(x.left, key);
        if(t != null)
            return t;
        else
            return x;
    }

    return ceiling(x.right, key);
}
```

### Selection

Seek the key of rank `k` (the key such that precisely `k` other keys in the BST are smaller). If the number of keys `t` in the left subtree is larger than `k`, we look (recursively) for the key of rank `k` in the left subtree; if `t` is equal to `k`, we return the key at the root; and if `t` is smaller than `k`, we look (recursively) for the key of rank `k - t - 1` in the right subtree.

![bst select]({{ site.url }}{{ site.baseurl }}/images/bst select.png)

```java
public Key select(int k){
    if(k < 0 || k >= size())
        return null;

    Node x = select(root, k);
    return x.key;
}

private Node select(Node x, int k) {
    if(x == null)
        return null;
    
    int t = size(x.left);
    if(t > k)
        return select(x.left, k);
    else if(t < k)
        return selelct(x.right, k - t - 1);
    else
        return x;
}
```

### Rank

If the given key is equal to the key at the root, we return the number of keys t in the left subtree; if the given key is less than the key at the root, we return the rank of the key in the left subtree; and if the given key is larger than the key at the root, we return t plus one (to count the key at the root) plus the rank of the key in the right subtree.

```java
public int rank（Key key) {
    return rank(key, root);
}

private int rank(Node x, Key key) {
    if(x == null)
        return 0;

    int cmp = key.compareTo(x.key);
    if(cmp < 0)
        return rank(key, x.left);
    else if(cmp > 0)
        return 1 + size(x.left) + rank(key, x.right);
    else
        return size(x.left);
}
```

### Delete the minimum and maximum

For delete the minimum, we go left until finding a node that that has a null left link and then replace the link to that node by its right link. The symmetric method works for delete the maximum.

![bst delete the minimum]({{ site.url }}{{ site.baseurl }}/images/bst deletemin.png)

```java
public void deleteMin() {
    if(isEmpty())
        throw new NoSuchElementException("Symbol table underflow");
    root = deleteMin(root);
}

private Node deleteMin(Node x) {
    if(x.left == null)
        return x.right;

    x.left = deleteMin(x.left);
    x.N = size(x.left) + size(x.right) + 1;
    return x;
}

public void deleteMax() {
    if(isEmpty())
        throw new NoSuchElementException("Symbol table underflow");
    root = deleteMax(root);
}

private Node deleteMax(Node x) {
    if(x.right == null)
        return x.left;

    x.right = deleteMax(x.right);
    x.N = size(x.left) + size(x.right) + 1;
    return x;
}
```

### Delete

Similar manner can be used to delete any node that has one child (or no children). The solution to delete a node that has two children, first proposed by T.Hibbard in 1962, is to delete a node `x` by replacing it with its `successor`. The `successor` is the node with the smallest key in the right subtree. We accomplish the task of replacing `x` by its successor in four easy steps.

{% capture notice %}
1. Save a link to the node to be deleted in `t`
2. Set `x` to point to its successor `min(t.right)`.
3. Set the right link of `x` (which is supposed to point to the BST containing all the keys larger than `x.key`) to `deleteMin(t.right)`, the link to the BST containing all the keys that are larger than `x.key` after the deletion.
4. Set the left link of `x` (which was null) to `t.left` (all the keys that are less than both the deleted key and its successor).
{% endcapture %}
<!-- notice, notice--primary, notice--info, notice--warning, notice--danger, notice--success -->
<div class="notice--info"> 
  {{ notice | markdownify }}
</div>

![bst delete]({{ site.url }}{{ site.baseurl }}/images/bst delete.png)

```java
public void delete(Key key) {
    root = delete(root, key);
}

private Node delete(Node x, Key key) {
    if(x == null)
        return null;

    int cmp = key.compareTo(x.key);
    if(cmp < 0)
        x.left = delete(x.left, key);
    else if(cmp > 0)
        x.right = delete(x.right, key);
    else {
        if(x.right == null)
            return x.left;
        if(x.left == null)
            return x.right;
         Node t = x;
         x = min(t.right);
         x.right = deleteMin(t.right);
         x.left = t.left;
    }
    x.N = size(x.left) + size(x.right) + 1;
    return x;
}
```

While this method does the job, it has a flaw that might cause performance problems in some practical situations. The problem is that the choice of using the successor is arbitrary and not symmetric. 

### Range search

To implement the `keys()` method that returns the keys in a given range, we begin with a basic recursive BST traversal method, known as `inorder traversal`. To illustrate the method, we consider the task of printing all the keys in a BST in order. To do so, print all the keys in the left subtree (which are less than the key at the root by definition of BSTs), then print the key at the root, then print all the keys in the right subtree, (which are greater than the key at the root by
definition of BSTs).

```java
private void print(Node x) {
    if(x == null)
        return;
    print(x.left);
    System.out.println(x.key);
    print(x.right);
}
```
