---
title: Hash Tables
categories: 
  - Course
  - Algorithm
tags:
  - Notes
---

Search algorithms that use hashing consist of 2 separate parts. The first step is to compute a *hash function* that transforms the search key into an array index. There is a possibility that two or more different keys may hash to the same array index. The second part of a hashing search is a *collision-resolution* process that deals with this situation.

![hashing crux]({{ site.url }}{{ site.baseurl }}/images/hashing crux.png)

## Hash functions

If we have an array that can hold M key-value pairs, then we need a function that can transform any given key into an index into that array: an integer in the range [0, M - 1]. We seek a hashing function that is both easy to compute and uniformly distributed the keys.

We have 3 primary requirements in implementing a good hash function for a given data type:

> It should be *deterministic* - equal keys must produce the same hash value.
>
> It should be *effecient* to compute.
>
> It should *uniformly distribute the keys*.

### Positive integers

The most commonly used method for hashing integers is called *modular hashing*: we choose the array size M to be prime, and for any positive integer key *k*, compute the remainder when dividing *k* by M. It's easy to implement and effective in dispersing the keys evenly between 0 and M - 1.

![modular hashing]({{ site.url }}{{ site.baseurl }}/images/modular hashing.png)

### Floating-point numbers

If the keys are real numbers between 0 and 1, intuitively, we can multiply the keys by M and round off to the nearest integer to get an index between 0 and M - 1. But this approach gives more weight to the most significant bits of the keys; the least significant bits play no role. One way to address this situation is to use modular hashing on the binary representation of the key.

### String

Modular hashing works for long keys such as strings, too. For instance, the following code computes a modular hash function for a String `s`, where `R` is a small prime integer.

```java
int hash = 0;
for (int i = 0; i < s.length(); i++) {
    hash = (R * hash + s.charAt(i)) % M;
}
```

### Compound keys

If the key type has multiple integer fields, we can typically mix them together in the way just described for `string` values.

### Java conventions

Every type of data needs a hash function are required to implement a mothod called `hashCode()` which returns a 32-bit integer. The implementation of `hashCode()` must be consistent with `equals()`.

### Converting a hashCode() to an array index

The code masks off the sign bit to turn the 32-bit integer to a 32-bit nonnegative integer and then computing the remainder when dividing by M.

```java
private int hash (Key key) {
    return (key.hashCode() & 0x7fffffff) % M;
}
```

## Hashing with separate chaining

The second component of a hashing algorithm is collision resolution: a strategy for handling the case when 2 or more keys to be inserted hash to the same index. A straightforward approach to collision resolution is to build, for each of the M array indices, a linked list of the key-value pairs whose keys  hash to that index. The basic idea is to choose M to be sufficiently large that the linked lists are sufficiently short to enable efficient search through a 2-step process: hash to find the list that could contain the key, then sequentially search through the list for the key.

```java
//Node first, a instance variable points to the first Node in the linked list
public Value get(Key key) {
    for (Node x = first; x != null; x = x.next) {
        if (key.equals(x.key))
            return x.val;
    }
    return null;
}

public void put(Key key, Value val) {
    if(val == null) {
        delete(first, key);
        return;
    }

    for (Node x = first; x != null; x = x.next) {
        if(key.equals(x.key)) {
            x.val = val;
            return;
        }
    }
    first = new Node(key, val, first);
    N++;
}

public Node detele(Node x, Key key) {
    if(x == null)
        return null;

    if(key.equals(x.key)) {
        N--;
        return x.next;
    }

    x.next = delete(x.next, key);
    return x;
}
```

![hashing with separate chaining]({{ site.url }}{{ site.baseurl }}/images/hashing with separate chaining.png)

## Hashing with linear probing

Another approach to implementing hashing is to store N key-value pairs in a hash table of size M > N, relying on empty entries in the table to help with collision resolution. Such methods are called *open-addressing* hashing methods. The simplest open-addressing method is called *linear probing*: when there is a collision (when we hash to a table index that is already occupied with a key different from the search key), then we just check the next entry in the table (by incrementing the index). There are 3 possible outcomes:

> key equal to search key: search hit.
>
> empty position (null key at indexed position): search miss.
>
> key not equal to search key: try nex entry.

```java
public Value get(Key key) {
    for (int i = hash(key); keys[i] != null; i = (i + 1) % M) {
        if (keys[i].equals(key))
            return vals[i];
    }
    return null;
}

public void put(Key key, Value val) {
    if(val == null) {
        delete(key);
        return;
    }

    if (N > M / 2)
        resize(2 * M);

    int i;
    for (i = hash(key); keys[i] != null; i = (i + 1) % M) {
        if (keys[i].equals(key)) {
            vals[i] = val;
            return;
        }
        keys[i] = key;
        vals[i] = val;
        N++;
    }
}

public void delete (Key key) {
    if(!contains(key))
        return;

    // find position i of key
    ini i = hash(key);
    while(!key.equals(keys[i])) {
        i = (i + 1) % M;
    }

    keys[i] = null;
    vals[i] = null;

    // rehash all keys after the deleted key
    i = (i + 1) % M;
    while (keys[i] != null) {
        Key key2Rehash = keys[i];
        Value val2Rehash = vals[i];
        keys[i] = null;
        vals[i] = null;
        N--;
        put(key2Rehash, val2Rehash);
        i = (i + 1 ) % M;
    }
    N--;

    if (N > 0 && N < M / 8)
        resize(M / 2);
}

private int hash(Key key) {
    return (key.hashCode() & 0x7fffffff) % M;
}
```

![hashing with linear probing]({{ site.url }}{{ site.baseurl }}/images/hashing with linear probing.png)

[Original Course Notes](http://algs4.cs.princeton.edu/34hash/)
