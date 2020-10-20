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

