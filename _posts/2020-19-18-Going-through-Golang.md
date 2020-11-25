# Going through some of the essential differences of Golang, to be able to start working on it within a few hours

I started learning Go for some system side software development. Trying to replicate a locally run mapreduce system. A lot was written about why Golang is very powerful in certain scenarios, but it is only when using and learning it I found how it combines some of the best features across several languages. Not an expert, but trying to learn some of the peculiarities, here are some notes that might be useful.

## First things I noticed

1. Go brings back pointers.
2. Go is a compiled language.
3. Garbage collection, which I don't really understand completely
4. Focus is on concurrency

## arrays and slices

1. Slices are similar to arrays, but not entirely same. Slices are dynamic arrays, which are designed with the aim of reducing copying overheads when increasing the size of an array. Slices are also default reference type, which means any varialbe assigned to a slice becomes a pointer and not a copy, which is not the case in case of arrays in Go. 

2. "..." behind slices which like ziping and unziping in python, very useful and widely used in Go


## Map and Struct

1. Maps are similar to slices when it comes to being reference types. Maps are passed by reference. Hence a variable assignment doesn't copy but becomes a pointer. So one needs to be careful.

2. If a key is not found in a Map, golang returns value of zero. To ensure that an item is present in a map or not, use another variable.

```
value , ispresent = map["key"]
```

3. When making an instance of a struct, it is important to keep in mind that the order of items of the struct instance is paramount, if the field type is not specified. It is better to use the field names.

4. Inheritance between two structs is done by embedding one struct into another. It is more of a has-a relationship and not is-a relationship. 

5. We use tags to struct fields, which are simiply string, and the logic can use them for validation.

## Defer

1. Since Go is built with concurrency in mind, it is possible that resources remain open, defer allows resource to be closed after all work is done.

## Pointers

Pointers behave like how they behave in C/C++. The Garbage collector makes it safer. 

## Functions

1. if a parameter in a function definition is like ...int, it means that it is expecting a wrapped input from the function call. So all the values will be wrapped and sent. This variable will act as a slice. If such a paramter is used, no other parameter can be used.

2. Go can return local variable as a pointer. 

3. Lot of synatactic sugar with the return, worth a note

4. Annonymous functions make a comeback


## 

