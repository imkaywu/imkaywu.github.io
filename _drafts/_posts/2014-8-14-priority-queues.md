---
title: Priority Queues
categories: 
  - Course
  - Algorithm
tags:
  - Notes
---

Priority queue is a data type supports 2 operations: **remove the maximum** and **insert**.

## API

![priority queue api]({{ site.url }}{{ site.baseurl }}/images/priority queue api.png)

## Elementary implementations

As discussed in [Implementations of stack, queue and bag](/2014/07/07/stack-queen-bag.html), priority queues can be implemented using the following data structure.

> *Array representation (unordered)*. The code for *insert* in the priority queue is the same as for the *push* in the stacks. To implement *remove the maximum*, we can exchange the maximum item with the last item and pop up this maximum item.
>
> *Array representation (ordered)*. When doing operation *insert*, always move the larger entries one position to the right, thus keep the array ordered.and the largested item is alwasy at the end.
>
> *Linked-list representations (unordered and reverse-ordered)*. Similarly, we can start with our linked-list code for pushdown stacks, either modifying the code for pop() to find and return the maximum or the code for push() to keep items in reverse order and the code for pop() to unlink and return the first (maximum) item on the list.

## Binary Heap

### Definition

A binary tree is *heap-ordered* if the key in each node is larger than (or equal to) the key in that nodes' two children (if any). The largest key in a *heap-ordered* binary tree is found at the root.

It's most convenient to impose the heap-ordering restriction on a *complete* binary tree.

![a heap-ordered complete binary tree]({{ site.url }}{{ site.baseurl }}/images/a heap-ordered complete binary tree.png)

The complete binary tree is represented sequentially within an array by putting the nodes with *level-order*.

![heap representations]({{ site.url }}{{ site.baseurl }}/images/heap representations.png)

In a heap, the parent of the node in position `k` is in position `k/2`, and  conversely, the two children of the node in position `k` are in positions `2k` and `2k+1`.

### Reheapify (restore heap order)

#### Bottom-up reheapify (swim)

Child's key is larger than that of the parent.

![swim up]({{ site.url }}{{ site.baseurl }}/images/swim up.png)

```java
private void swim(int k) {
    while(k > 1 && less(k / 2, k)) {
        exch(k, k / 2);
        k = k / 2;
    }
}
```

#### Top-down heapify(sink)

Parent's key is smaller than that of either of the children.

![sink down]({{ site.url }}{{ site.baseurl }}/images/sink down.png)

```java
private void sink(int k) {
    while(2 * k <= N) {
        int j = 2 * k;
        if(less(j, j + 1))
            j++; // j = 2 * k + 1;
        if(!less(k, j))
            break;
        exch(k, j);
        k = j;
    }
}
```

## Insertion and removal of heap-based priority queue

### Insert

Add the new item at the end of the array, increment the size of the heap, then swim up through the heap with that item to restore the heap condition.

### Remove tha maximum

Exchange the maximum item (at the root) with the last item, decrement the size of the heap, then sink down through the heap with the root item to restore the heap condition.

![heap operations]({{ site.url }}{{ site.baseurl }}/images/heap operations.png)

[Original Course Notes](http://algs4.cs.princeton.edu/24pq/)
