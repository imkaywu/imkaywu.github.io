---
title: Heapsort
categories: 
  - Course
  - Algorithm
tags:
  - Notes
---

Heapsort breaks into 2 phases: **heap construction**, which reorganizes the original array into a heap, then **sortdown**, which pull items out of the heap in decreasing order to build the sorted result. Doing so allows us to sort an array without needing any extra space.

### Heap construction

#### left - right with swim()

Use `swim()` to ensure that the entries to the left of the scanning pointer make up a heap-ordered complete tree, like successive priority queue insertions.

#### right - left with sink()

Every position in the array is the root of a small subheap; `sink()` works or such subheaps, as well. If the two children of a node are heaps, then calling `sink()` on that node makes the subtree rooted there a heap.

### Sortdown

Remove the largest remaining items from the heap and put it into the array position vacated as the heap shrinks.

![Heapsort]({{ site.url }}{{ site.baseurl }}/images/heapsort.png)

```java
public static void sort(Comparable[] pq) {
    int leng = pq.length;
    for(int i = leng / 2; i >= 1; i--) {
        sink(pq, i, leng);
    }

    while(leng > 1) {
        exch(pq, 1, leng--);
        sink(pq, 1, leng);
    }
}
```

<!-- [Original Course Notes]() -->
