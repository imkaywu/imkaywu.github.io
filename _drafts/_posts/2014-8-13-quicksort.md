---
title: Quicksort
categories: 
  - Course
  - Algorithm
tags:
  - Notes
---

## About the algorithm

Quicksort is a divide-and-conquer method for sorting. It works by partitioning an array into two parts, then sorting the parts independently.

The key of the method is the partitioning process, which rearranges the array to make the following three conditions hold:

> The entry `a[j]` is in its final place in the array, for some `j`.
>
> No entry in `a[lo]` through `a[j - 1]` is greater than `a[j]`.
>
> No entry in `a[j + 1]` through `a[hi]` is less than `a[j]`.

This is a randomized algorithm, because it randomly shuffles the array before sorting it.

![quicksort overview]({{ site.url }}{{ site.baseurl }}/images/quicksort overview.png)

## Partitioning

> Arbitrarily choose `a[lo]` to be the partitioning item.
>
> scan from the left end of the array until finding an entry that is greater than (or equal to) the partitioning item, then scan from the right end of the array until finding an entry less than (or equal to) the partitioning item.
>
> The two items that stopped the scans are out of place in the final partitioned array, so exchange them.
>
> When the scan indices cross, complete the partitioning process by exchanging the partitioning item `a[lo]` with the rightmost entry of the left subarray (`a[j]`) and return its index `j`.

![quicksort partitioning overview]({{ site.url }}{{ site.baseurl }}/images/quicksort partitioning overview.png)

![quicksort partitioning trace]({{ site.url }}{{ site.baseurl }}/images/quicksort partitioning trace.png)

```java
private static int partition(Comparable[] a, int lo, int hi) {
    int i = lo;
    int j = hi + 1;
    Comparable v = a[lo];
    while(true) {
        while(less(a[++i], v)) // find an item greater than or equal to v
            if(i == hi) break;

        while(less(v,a[--j])) // find an item less than or equal to v
            if(j == lo) break;

        if(i >= j) // the partition is finished
            break;

        exch(a, i, j);
    }

    exch(a, lo, j);
    
    return j;
}

private static void sort(Comparable[] a, int lo, int hi) {
    if(hi <= lo)
        return;

    int j = partition(a, lo, hi);
    sort(a, lo, j - 1);
    sort(a, j + 1, hi);
    asset isSorted(a, lo, hi);
}

public static void sort(Comparable[] a) {
    // shuffle array a
    sort(a, 0, a.length - 1);
}
```

## Entropy-optimal sorting

This is applied to arrays having large numbers of duplicate sort keys with potential to reduce the time of the sort from linearithmic to linear.

Partitioning the array into three parts with a pointer `lt` such that `a[lo..lt - 1]` is less than `v`, a pointer `gt` such that `a[gt + 1..hi]` is greater than `v`, and a pointer `i` such that `a[lt..i - 1]` are equal to `v`, and `a[i..gt]` are not yet examined.

> `a[i]` less than `v`: exchange `a[i]` with `a[lt]` and increment both `i` and `lt`.
>
> `a[i]` greater than `v`: exchange a[i] with `a[gt]` and decrease `gt`.
>
> `a[i]` equal to `v`: increase `i`.

![3 way partitioning overview]({{ site.url }}{{ site.baseurl }}/images/3 way partitioning overview.png)

![3 way partitioning trace]({{ site.url }}{{ site.baseurl }}/images/3 way partitioning trace.png)

```java
private static void sort(Comparable[] a, int lo, int hi) {
    if(hi <= lo)
        return;

    int lt = lo;
    int i = lo;
    int gt = hi;
    Comparable v = a[lt];
    while(i <= hi) {
        int cmp = a[i].compareTo(v);
        if(cmp < 0)
            exch(a, lt++, i++);
        else if(cmp > 0)
            exch(a, i, gt--);
        else
            i++;
    }

    sort(a, lo, lt - 1);
    sort(a, gt + 1, hi);

    assert isSorted(a, lo, hi);
}

public static void sort(Comparable[] a) {
    // shuffle array a;
    sort(a, 0, a.length - 1);
    assert isSorted(a, 0, a.length - 1);
}
```

[Original Course Notes](http://algs4.cs.princeton.edu/23quicksort/)
