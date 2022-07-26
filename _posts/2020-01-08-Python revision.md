---
title: Python revision
subtitle: Some basic stuff about python that is important
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
  - Call-by-Object-Reference
  - Functions
  - 
published: true
---

# Python data structures and classes

* Python is an interpreted language
* Python is completely object oriented
* unlike C++ and Java, which have long, short for int, Python chooses the internal representation based on magnitude of its value.
* Binary number start as 0b100101, Octal as 0o342, Hexadecimal as 0x7f
* If string is passed to the int() constructor, it should be a number e.g ("123"), by default it should be decimal, to specify binary,octal,hexadecimal, pass it as ('7f',16)
* Float class is the only floating-point type in Python. It uses [fixed-point representation](https://www.tutorialspoint.com/fixed-point-and-floating-point-number-representations) 
* Strings can be passed to the float constructor, if they are floating point or floating point literals. i.e float("3.14") and float("2.0e3") will be succesfully parsed and converted to floating point numbers
* float doesn't take binary, Octal or hexadecimal inputs
* str class used for representing immutable, iterable strings
* use **\\** for escape characters like ",'

## Python lists

* list() construtor accepts any parameter of **iterable** type.
* l = [1,2,3,4], k = l means k and l both point to the same list, and k[2] will change l[2]
* Similar to arrays, but lists are dynamic. Implemented as circular linked lists, hence elements from the end can be accessed with a -, for example l[-1] will give last element, l[-2] will produce second last element
* collections.dequeue() often useful is only first and last elements are useful

## Tupes in Python

* Tuples are immutable lists

## Set class in Python

* set is [implemented](https://www.geeksforgeeks.org/internal-working-of-set-in-python/) using hash map in python
* set only accepts items of immutable type
* since lists are not immutable, one cannot have a set of lists or set of sets, but rather a set of tuples
* for immutable sets use frozensets

## Dict class in Python

* Used to store data in a key:value form
* collections.counter() is often useful
* used as a hashmap

# [Operators](https://python-reference.readthedocs.io/en/latest/docs/operators/)

* // is used for floor division
* ** used for power
* Callable operators
  - *(tuple packing)
  - *(tuple unpacking)

# Conditionals

* [if-else](https://www.scaler.com/topics/python/python-if-else-statement/)
* if elif else
* for, while
* break, continue

# Functions in Python

* Can return multiple items
* Functions argument passing is neither call-by-reference, nor call-by-value. [They are call-by-object reference](https://jeffknupp.com/blog/2012/11/13/is-python-callbyvalue-or-callbyreference-neither/)
* Python objects are not copied, this is oftne useful. Because of the call-by-object-reference model, mutable and immutable class of primitive data structures are very important to know. Passing mutable data types to functions should be done carefully.
* functions can contain keyword arguments: ``` def sum(a=0,b=0):```. Here a and b are keyword arguments.

# Exception Handling in Python

* raise <Errortype>("<Error message>")
* alternatively, 
```
try:
  ratio = x/y

except DivdeByZeroError:
  print("<error message>")
```


# Other important notes:

1. Lists are mutable, tuples are immutable
2. Python function are first-class objects, which allows them to be assigned to variables and be passed to functions
3. PEP-8 is a popular style guide to writing python code
4. Python has an array datatype as well, needs to be import, can hold elements of same datatype only unlike lists
5. lambda functions are annonymous functions
6. An iterable is an object that has an __iter__ method which returns an iterator, or which defines a __getitem__ [method that can take sequential indexes](https://thispointer.com/python-how-to-make-a-class-iterable-create-iterator-class-for-it/) starting from zero (and raises an IndexError when the indexes are no longer valid). So an iterable is an object that you can get an iterator from. An iterator is an object with a __next__  method.
7. Packing and upacking using *args and **kwargs is a very elegant way to processing in Python. *args unpacks into a tuple, and **kwargs unpacks into a dictionary. It is not neccessary to use those names.
8. help() is a default method used to print docstring, dir() is a default method that returns all the attributes for an object, very useful for debugging
9. Also useful for debugging is logging module and debugger
10. map() function is used to iterate over a list and perform operation on it
11. python debugger is pdb
12. Generators are simple functions which return an iterable set of items, one at a time
13. In Python a variable is not an alias for a location in memory. Rather, it is simply a binding to a Python object

