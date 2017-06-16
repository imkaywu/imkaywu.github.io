---
title: Mergesort
categories: 
  - Course
  - Algorithm
tags:
  - Notes
---

**How-to**: To sort an array, divide it into two halves, sort two halves (recursively), and then merge the results.

![mergesort overview]({{ site.url }}{{ site.baseurl }}/images/mergesort overview.png)

## Abstract in-place merge

The method `merge(a, lo, mid, hi)` puts the results of merging the subarrays `a[lo..mid]` with `a[mid + 1..hi]` into a single ordered array, leaving the result in `a[lo..hi]`.

![Abstract in-place merge]({{ site.url }}{{ site.baseurl }}/images/abstract in-place mergesort.png)

## Top-down mergesort

It's a recursive mergesort implementation based on the abstract in-place merge. A typical **divide-and-conquer** paradigm.

```java
private static void merge(Comparable[] a, Comparable[] aux, int lo, int mid, int hi) {
    // precondition: a[lo..mid] and a[mid + 1..hi] are sorted subarrays
    assert isSorted(a, lo, mid);
    assert isSorted(a, mid + 1, hi);

    // copy to aux[]
    for (int k = lo; k <= hi; k++){
        aux[k] = a[k];
    }

    // merge back to a[]
    int i = lo;
    int j = mid + 1;
    for (int k = lo; k <= hi; k++) {
        if (i > mid) {
            a[k] = aux[j++];
        }
        else if (j > hi) {
            a[k] = aux[i++];
        }
        else if (less(aux(i), aux(j)) {
            a[k] = aux[i];
        }
        else {
            a[k] = aux[j];
        }
    }

    // postcondition: a[lo..hi] is sorted
    asset isSorted(a, lo, hi);
}

// mergesort a[lo..hi] using auxiliary array aux[lo..hi]
private static void sort(Comparable a[], Comparable aux[], int lo, int hi) {
    if(hi <= lo) {
        reture;
    }
    int mid = (lo + hi) / 2;
    sort(a, aux, lo, mid);
    sort(a, aux, mid + 1, hi);
    merge(a, aux, lo, hi);
}

/**
* Rearranging the array in ascending order, using the natural order.
* @param a the array to be sorted
*/
public static void sort(Comparable a[]) {
    Comparable aux[] = new Comparable[a.length];
    sort(a, aux, 0, a.length - 1);
    assert isSorted(a);
}
```

![top-down mergesort]({{ site.url }}{{ site.baseurl }}/images/top-down mergesort.png)

## Bottom-up mergesort

Another way to implement mergesort is to organize the merges so that we do all the merges of tiny arrays on one pass, then do a second pass to merge those arrays in pairs, and so forth, continuing until we do a merge that encompasses the whole array. We start by doing a pass of 1-by-1 merges (considering indicidual items as subarrays of size 1), then a pass of 2-by-2 merges (merge subarrays of size 2 to make subarrays of size 4), then 4-by-4 merges, and so forth.

```java
private static void merge(Comparable[] a, Comparable[] aux, int lo, int mid, int hi) {
    // same as top-down merge
}

/**
* Rearranges the array in ascending order, using the natural order.
* @param a the array to be sorted
*/
public static void sort(Comparable[] a) {
    int N = a.length;
    Comparable[] aux = new Comparable[N];
    for(int n = 1; n < N; n = 2 * n) {
        for(int i = 0; i < N - n; i += 2 * n) {
            int lo = i;
            int mid = i + n - 1;
            int hi = Math.min(i + 2 * n - 1, N - 1);
            merge(a, aux, lo, mid, hi);
        }
    }
}

// My implemetation of the sort
public static void sort(Comparable[] a) {
    int leng = a.length;
    int mergeSize = 1;
    Comparable[] aux = new Comparable[a.length];
    while(mergeSize <= a.length) {
        for(int i = 0; i < leng; i += mergeSize) {
            int lo = i;
            int hi = Math.min(i + mergeSize - 1, leng - 1);
            int mid = (lo + hi) / 2;
            merge(a, aux, lo, mid, hi);
        }
    }
}
```

![bottom-up mergesort]({{ site.url }}{{ site.baseurl }}/images/bottom up mergesort.png)

## Improvements to top-down mergesort

> Use insertion sort for small subarrays. We can improve most recursive algorithms by handling small cases differently. Switching to insertion sort for small subarrays will improve the running time of a typical mergesort implementation by 10 to 15 percent.
>
> Test whether array is already in order. We can reduce the running time to be linear for arrays that are already in order by adding a test to skip call to merge() if a[mid] is less than or equal to a[mid+1]. With this change, we still do all the recursive calls, but the running time for any sorted subarray is linear.
>
> Eliminate the copy to the auxiliary array. It is possible to eliminate the time (but not the space) taken to copy to the auxiliary array used for merging. To do so, we use two invocations of the sort method, one that takes its input from the given array and puts the sorted output in the auxiliary array; the other takes its input from the auxiliary array and puts the sorted output in the given array. With this approach, in a bit of mindbending recursive trickery, we can arrange the recursive calls such that the computation switches the roles of the input array and the auxiliary array at each level.

```java
// src is a clone of dst, src = dst.clone();

private static void merge(Comparable[] src, Comparable[] dst, int lo, int mid, int hi) {
    // precondition: src[lo..mid] and src[mid + 1..hi] are sorted subarrays
    assert isSorted(src, lo, mid);
    assert isSorted(src, mid + 1, hi);
    }

    int i = lo;
    int j = mid + 1;
    for (int k = lo; k <= hi; k++) {
        if (i > mid) {
            dst[k] = src[j++];
        }
        else if (j > hi) {
            dst[k] = src[i++];
        }
        else if (less(src(i), src(j)) {
            dst[k] = src[i];
        }
        else {
            dst[k] = src[j];
        }
    }

    // postcondition: dst[lo..hi] is sorted
    asset isSorted(dst, lo, hi);
}

private static void sort(Comparable[] src, Comparable[] dst, int lo, int hi) {
    if(hi <= lo + CUTOFF) { // CUTOFF = 7, improvement 1
        insertionSort(dst, lo, hi);
        return;
    }

    int mid = (lo + hi) / 2;
    sort(dst, src, lo, mid);
    sort(dst, src, mid + 1, hi)

    if(!less(src[mid + 1], src[mid])) { // improvement 2
        System.arraycopy(src, lo, dst, lo, hi - lo + 1);
        return
    }

    merge(src, dst, lo, mid, hi);
}
```

[Original Course Notes](http://algs4.cs.princeton.edu/22mergesort/)
