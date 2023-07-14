#BitWise problems in Python: Python only has bitwise operators working on Integers
## Patterns

Generally if a problem can be solved using bitwise, it is quite fast. Proofs for bitwise solutions is tricky to come up with. If the solution required is too good to be true, like O(1) space and O(n) time, it could be a bitwise approach. 

Using Python for DSA interviews seems like a boon, until we encounter things like bitwise and string manipulation. Especially string manipulation, since Python doesn't have something like Java StringBuilder. 

Talking about Bitwise, the advantage C++ BitSet and Java BitSet provide 
1. Better readability and explainability of the code
2. Manipulation and access to individual bits using member functions: set(), flip(), etc
3. Lot of internal optimization(don't know specifics, but compiler level optimizations)

Python uses two's complement representation. But some methods like bin() return binary equivalent in sign-magnitude. 

https://stackoverflow.com/questions/55145028/binary-ones-complement-in-python-3#:~:text=As%20confirmed%20in%20Python%20docs,operation%20on%20their%20absolute%20value

## Some important code that should be readilty avaliable

### Two's complement of an Integer for a given size, e.g 32 bit , put diagram

### Hamming weight : Counting number of 1's. (Using XOR)

### Gray Code: Very relevant, put with diagram

### Brian Kernighan’s Algorithm

### Single Number 1,2,3

More resources: https://medium.com/techie-delight/bit-manipulation-interview-questions-and-practice-problems-27c0e71412e7