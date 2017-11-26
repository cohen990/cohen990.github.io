---
layout: post
title: "Data Structures - Heap"
date: 2015-10-27 16:25:06
description: An introduction to heaps. Why and how you would use them.
tags: 
- data-structures
- algorithms
- optimisation
- computer-science
---
# Data Structures - Heap

Data structures are important. Along with algorithms, they are the underpinnings of anything we do in programming.

Let's say you have a list of numbers between 1 -> 1000. Let's say also that all you ever need to do with them, is query the largest. Sounds like you'll be using something like

```
int max = 0;
for(int i = 0; i < myArray.length; i++){
    if(myArray[i] > max){
        max = myArray[i];
    }
}
```

In order to find the max value, your computer will have to iterate through the entire array. For an array of length 'n', that is an O(n) operation (this is [Big O notation](https://en.wikipedia.org/wiki/Big_O_notation)) which means that for 50,000 items in an array, the search will take some multiple of 50,000 steps to find the max.  

Let's say you want to find and remove the largest element from myArray. What then?  

```
int maxIndex = 0;
for(int i = 0; i < myArray.length; i++){
    if(myArray[i] > myArray[maxIndex]){
        maxIndex = i;
    }
}
for(int i  = maxIndex; i < myArray.length - 1; i++){
    myArray[i] = myArray[i+1];
}
myArray[myArray.length - 1] = Integer.MIN_VALUE;

```
  
It's getting quite lengthy, isn't it. Not to mention, the time complexity for this find and remove operation is still O(n).  

## What is a Heap?

A heap is a data structure in which the top element is the largest (or, for a min-heap, the smallest) value within the heap. Each element has two children and each Node can be split off to be a new Heap.

![alternative text](https://upload.wikimedia.org/wikipedia/commons/thumb/3/38/Max-Heap.svg/480px-Max-Heap.svg.png)

So:
1.  The root of each heap is the largest value in that heap
2.  Each node has two children
3.  Each node is a heap in its own right

This means that each node is larger than all of its children.

## How does this relate to our problem?

**The Heap.max() operation for a max-heap executes in O(1) time. The Heap.pop() (equivalent to our find and remove the largest element) operation executes in O(log n) time.**

For these two operations,  if those are the only operations you need, the Heap has superior lookup times and because under the hood it can be implemented as an array, it has a very similar memory footprint to the array implementation we used before.

My implementation of a heap can be found [on github](https://github.com/cohen990/Heap).

## Time Complexity Analysis - Max

These concerns become important when dealing with very large data sets.

Let's say:
- the array lookup takes 'c*n' operations to resolve where 'n' is the size of the array and 'c' is some constant.
- the heap lookup takes 'd' operations to resolve.
So:
- these two operations to take equal time when: 'c*n = d'
- therefore they take equal time when 'n = d/c'
- if we assume that a modern computer can manage about 100 GFLOPS, we can calculate roughly how long each implementation takes

For the array, the max operation (assuming no optimizations) takes 3 * n + 4 operations to find and return the max value.

If we take n to an extremely high value e.g. 1,000,000,000,000, the operation will take:

```
(3 * 1,000,000,000,000 + 4) / 100,000,000,000 = 30 seconds
```

However, the heap operation with 1 trillion values in the heap, will still only take d operations.

In my implementation, d = 2\. This takes 

```
2/100,000,000,000 = 20 picoseconds
```

###Time Complexity Analysis - Pop

The pop operation is similar - I will ignore c and d in this one though.

For 1 trillion values, the array implementation takes roughly 

```
1,000,000,000,000 / 100,000,000,000 = 10 seconds
```

The heap implementation takes roughly 

```
log(1,000,000,000,000) / 100,000,000,000 = 398 picoseconds  
```

### Summary

So it's clear that the heap is superior in situations where only the max or the min values are required: especially when working with very large data sets.

Further discussions of the actual implementations of a heap are coming. Also about big O notation.
