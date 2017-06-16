---
title: Implementations of stack, queue and bag
categories:
  - Dev
  - Course
tags:
  - Algorithm
---

Stack, queue and bag are data types used to store a collection of objects with generic types. The operations include adding, removing or iterating objects.

## Introduction

* *Bags*. A bag is a collection where removing items is not supported—its purpose is to provide clients with the ability to collect items and then to iterate through the collected items.  
* *Queue*. A FIFO queue is a collection that is based on the first-in-first-out (FIFO) policy.  
* *Stack*. A pushdown stack is a collection that is based on the last-in-first-out (LIFO) policy. 

## API
![api of stack, queen and bag]({{ site.url }}{{ site.baseurl }}/images/api_of_stack_queen_bag.png)

## Implementation

Linked-list or resizing array are used to implement these data structures. A linked list is a recursive data structure that is either empty (null) or a reference to a node having a generic item and a reference to a linked list. To implement a linked list, we start with a nested class that defines the node abstraction

```java
private class Node {
    Item item;
    Node next;
}
```

### Stack

#### Linked-list

##### Push

The *node first* is the latest pushed object.

![linked-list stack, push operation]({{ site.url }}{{ site.baseurl }}/images/linked_list_stack_push.png)

##### Pop

The *node first* is the object needed to be popped out.

![linked-list_stack_pop operation]({{ site.url }}{{ site.baseurl }}/images/linked_list_stack_pop.png)

#### Array

##### Array with capacity

![array_stack_with_capacity]({{ site.url }}{{ site.baseurl }}/images/array_stack_with_capacity.png)

##### Sizing array

* push(): double size of array when array is full.  
* pop(): halve size of array when array is **one-quarter full**.

### Queue

#### Linked-list

##### Enquene

The *node first* is the first object added to the queen. The *node last* is the latested object added to the queue.

![linked_list_queue_enqueue_operation]({{ site.url }}{{ site.baseurl }}/images/linked_list_queue_enqueue.png)

##### Dequeue

The *node first* is the first object added to the queen and first to be popped out of the queue.

![linked_list_queue_dequeue_operation]({{ site.url }}{{ site.baseurl }}/images/linked_list_queue_dequeue.png)

#### Array

##### Array with capacity

![array_queue_with_capacity]({{ site.url }}{{ site.baseurl }}/images/array_queue_with_capacity.png)

##### Sizing array

* how to resize?

[Original Course Note](http://algs4.cs.princeton.edu/13stacks/)
