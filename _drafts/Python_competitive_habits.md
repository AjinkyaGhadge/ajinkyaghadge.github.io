---
title: Python habbits for Coding Interviews
subtitle: Tools are as good as the master, but you can take a knife to a sword fight!
gh-repo: https://github.com/AjinkyaGhadge/Code_Revision
gh-badge:
  - star
  - fork
  - follow
tags:
  - DSA
  - Data Structures and Algorithms
  - Python
  - Python3
  - Trees
  - Binary Search Tree
published: true
---

# Overlooked basics

(learn all the implementations of common datastructures in python)

* Know all methods of list very well, it is circular and linked


# Data Structures

* Collections
* List
* Set
* Dictionary



# Misc

* Zip
* itertools (very powerful and useful)
  - Combinations
  - Permutations
  - chain

* lambda, map and list comprehensions and which to really use
* fstring
* Generators for saving memory and time (only suitable for certain situations)
* understanding and using [callable operators](https://python-reference.readthedocs.io/en/latest/docs/operators/#callables-operators)



# Tricks:

* transposing a matrix 
```
mat = [[1, 2, 3], [4, 5, 6]]
zip(*mat)
[(1, 4), (2, 5), (3, 6)]

```

* Iterate With enumerate() Instead of range()

* Debug With breakpoint() Instead of print()

* When using a set, rather than making a list and converting to a set, start by taking an empty set and directly add data to the set

* https://wiki.python.org/moin/PythonSpeed/PerformanceTips

* https://www.geeksforgeeks.org/python-tricks-competitive-coding/

* Use if condition1 & condition2 instead of if condition1: if condition2

* Know how to sort dictionaries by key, value and by value and then key and key and then value

* Sort a list by [nth element in a sub-list](https://stackoverflow.com/questions/17555218/python-how-to-sort-a-list-of-lists-by-the-fourth-element-in-each-list) also look at [this](https://leetcode.com/problems/matrix-cells-in-distance-order/discuss/478365/Python-C%2B%2B-easy-sort-solution) question on leetcode.

* 