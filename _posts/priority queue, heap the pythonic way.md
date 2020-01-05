---
title: DSA Revision - Priority Queue, Heaps
subtitle: A revision of widely used adaptations of trees for specific problems
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
# Priority Queue

## Priority Queue using lists and sorting

In several situations were the concept of First In First Out(FIFO) is used, there can also be an added weight to the queueing. I will call it weighted queueing for the sake of our onvinience. The criteria that a queue has are as follows:

1. An element with high priority is dequeued before an element with low priority
2. If both elements have same priority, then use FIFO logic

For each element, there has to be a way to represent/store its priority. A weight could also be obtained say based on output from a machine learning model. 

For the purpose of implementation, we will assume that the value of node itself represents the weight.

### Trivial implementation using an unsorted list:

```
class PriorityQueue(object): 
    def __init__(self): 
        self.q = [] 
  
  
    # for checking if the queue is empty 
    def isEmpty(self): 
        return (len(self.q) == 0)
  
    # for inserting an element in the queue 
    def insert(self, data): 
        self.q.append(data)
  
    # for popping an element based on Priority 
    def delete(self): 
        pelement = max(self.q)
        self.q.remove(pelement)
        return pelement
  
if __name__ == '__main__': 
    myQueue = PriorityQueue() 
    myQueue.insert(12) 
    myQueue.insert(1) 
    myQueue.insert(14) 
    myQueue.insert(7) 
    print(myQueue)             
    while not myQueue.isEmpty(): 
        print(myQueue.delete())
```

Output :

```
<__main__.PriorityQueue object at 0x7fe794bc7390>
14
12
7
1
```

### Trivial implementation using a sorted list:

I used the [bisect](https://docs.python.org/3/library/bisect.html#module-bisect) module here for [performing sorting and insertion effectively](https://stackoverflow.com/questions/8024571/insert-an-item-into-sorted-list-in-python).

```
# Priority Queue Implementation using sorted list
import bisect 

class PriorityQueue(object): 
    def __init__(self): 
        self.q = [] 
  
    # for checking if the queue is empty 
    def isEmpty(self): 
        return (len(self.q) == 0)
  
    # for inserting an element in the queue 
    def insert(self, data): 
        bisect.insort(self.q,data)
  
    # for popping an element based on Priority 
    def delete(self): 
        if len(self.q)!= 0:
            pelement = self.q[-1]
            self.q.remove(self.q[-1])
            return pelement
        else:
            raise ValueError("List is empty")
  
if __name__ == '__main__': 
    myQueue = PriorityQueue() 
    myQueue.insert(12) 
    myQueue.insert(1) 
    myQueue.insert(14) 
    myQueue.insert(7) 
    print(myQueue)             
    while not myQueue.isEmpty(): 
        print(myQueue.delete())  

```

Output:

```
<__main__.PriorityQueue object at 0x7fe794bc7390>
14
12
7
1
```
## Priority Queue using Heap

### Heap Properties

* For every node x(each node contains a key and a value) with parent p, the key in p is always less than or equal to the key in x.



```
class BinaryHeap:

    def __init__(self):
        self.heaplist = [0]
        self.currentsize = 0
        
    def _position_node(self,heapsize):
        while heapsize//2 > 0:
            if self.heaplist[heapsize//2]>self.heaplist[heapsize]:
                self.heaplist[heapsize//2],self.heaplist[heapsize] = self.heaplist[heapsize],self.heaplist[heapsize//2]
            heapsize = heapsize//2 
    
    def insert(self,data):
        self.heaplist.append(data)
        self.currentsize+=1
        self._position_node(self.currentsize)

    def _position_root(self,i):
        while i*2 <= self.currentsize:
            if i * 2 + 1 > self.currentsize:
                loc = i * 2
            else:
                if min(self.heaplist[i*2],self.heaplist[i*2+1]) == self.heaplist[i*2]:
                    loc = i*2
                else:
                    loc = i*2+1
            if self.heaplist[i] > self.heaplist[loc]:
                self.heaplist[i],self.heaplist[loc] = self.heaplist[loc],self.heaplist[i]
            i = loc
    
    def pop(self):
        data = self.heaplist[1]
        self.heaplist[1] = self.heaplist[self.currentsize]
        self.currentsize-=1
        self.heaplist.pop()
        self._position_root(1)
        return data

    def heapfromlist(self,arr:list):
        i = len(arr)//2
        self.currentsize = len(arr)
        self.heaplist = [0]+arr
        while i > 0:
            self._position_root(i)
            i-=1

heapvals = BinaryHeap()
heapvals.heapfromlist([10,5,8,2,3])

print(heapvals.pop())
print(heapvals.pop())
print(heapvals.pop())
print(heapvals.pop())
print(heapvals.pop())

```

Output:


```
2
3
5
8
10
```



# Credits
1. [priority queue implementation](https://runestone.academy/runestone/books/published/pythonds/Trees/BinaryHeapImplementation.html)
2. [Stackoverflow](https://codereview.stackexchange.com/questions/197040/min-max-heap-implementation-in-python)
3. [Why is binary heap preffered over BST for Priority Queue](https://www.geeksforgeeks.org/why-is-binary-heap-preferred-over-bst-for-priority-queue/)