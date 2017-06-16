---
title: Elementary Symbol Table
categories: 
  - Course
  - Algorithm
tags:
  - Notes
---

A *symbol table* is a data structure that associates a *value* with a *key*. It supports 2 primary operations: *insert (put)* a new pair into the table and *search for (get)* the value associated with a given key. 

## API

![symbol table api]({{ site.url }}{{ site.baseurl }}/images/symbol table api.png)

### Generics

The keys and values are not specified using generics.

### Duplicate keys

No duplicate keys in a table. When the key-value pair was put into a table already containing that key, the associated value is replaced.

### Null values

No keys can be associated with `null`. This convention has 2 consequences:

> test whether or not the symbol table defines a value associated with a given key by testing whether `get()` return `null`.
>
> call `put()` with `null` as its second argument to implement deletion.  

### Iterators

The `keys()` method returns an `Iterable<Key>` object to iterate through the keys.

### Key equality

> *Reflexive*: `x.equals(x)` is true.
>
> *Symmetric*: `x.equals(y)` is true if and only if `y.equals(x)` is true.
>
> *Transitive*: if `x.equals(y)` and `y.equals(z)` are true, then so is `x.equals(z)`.
>
> *Consistency*: multiple invocation of `x.equals(y)` consistently return the same value, provided neither object is modified.
>
> *Not null*: `x.equals(null)` returns `false`.

## Ordered symbol table

Several symbol table implementations take advantage of order among the keys to provide efficient implementations of the `put()` and `get()` operations. 

### API

![ordered symbol table api]({{ site.url }}{{ site.baseurl }}/images/ordered symbol table api.png)

## Elementary implementations

### Sequential search in an unordered linked list

> `get()`. Scan through the list, using `equals()` to compare the search key with the key in each node in the list. If we find the match, we return the associated value; if not, we return `null`.
>
> `put()`. Scan through the list, using `equals()` to compare the client key with the key in each node in the list. If we find the match, we update the value associated with that key to be the value given in the second argument; if not, we create a new node with the given key and value and insert it at the begining of the list. This is **Sequential search**.

![linked-list symbol table]({{ site.url }}{{ site.baseurl }}/images/linked-list symbol table.png)

### Binary search in an ordered array

The underlying data structure is 2 parallel arrays with the keys kept in order.The heart of the implementation is the `rank()` method, which returns the number of keys smaller than a given key.

> `get()`. The `rank()` tells us precisely where the key is to be found if it's in the table.
>
> `put()`. The `rank()` tells us precisely where to update the value when the key is in the table, and precisely where to put the key when the key is not in the table (need to move all larger keys over one position to the right)

![ordered array symbol table]({{ site.url }}{{ site.baseurl }}/images/ordered array symbol table.png)

#### Binary search

The reason to keep keys in an ordered array is to use *binary search* to dramatically reduce the number of compares.

![binary search symbol table]({{ site.url }}{{ site.baseurl }}/images/binary search symbol table.png)

```java
public int rank(Key key) {
    int lo = 0;
    int hi = N - 1;
    while(lo <= hi) {
        int m = (lo + hi) / 2;
        int cmp = key.compareTo(keys[m]);
        if(cmp < 0)
            hi = m - 1;
        else if(cmp > 0)
            lo = m + 1;
        else
            return m;
    }
    return lo;
}
```

[Original Course Notes](http://algs4.cs.princeton.edu/31elementary/)
