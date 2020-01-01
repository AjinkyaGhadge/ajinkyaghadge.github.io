---
title: A Simple Implementation of Binary Search Tree
subtitle: What better than to build a tree from scratch!
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
# Binary Search Tree

> Binary search tree is also called an ordered binary tree.

## Properties:
* The left subtree of a node contains only nodes with keys less than the node’s key.
* The right subtree of a node contains only nodes with keys greater than the node’s key.
* Both the left and right subtrees must also be binary search trees.


## Implementation:

### Node Class:

```
class Node:
    def __init__(self,data,left=None,right=None):
        self.left = left
        self.right = right
        self.data = data
```

### Implementation

```
class Node:
    def __init__(self,data,left=None,right=None):
        self.data = data
        self.left = left
        self.right = right

    def insert(self, data):
        if self.data:
            if data < self.data:
                if self.left is None:
                    self.left = Node(data)
                else:
                    self.left.insert(data)
            elif data > self.data:
                if self.right is None:
                    self.right = Node(data)
                else:
                    self.right.insert(data)
        else:
            self.data = data
    
    def lookup(self,data,parent=None):
        if data<self.data:
            if self.left is None:
                return None,None
            else:
                return self.left.lookup(data,self)
        elif data>self.data:
            if self.right is None:
                return None,None
            else:
                return self.right.lookup(data,self)
        else:
            return self,parent

    def delete(self,data):
        a , b = self.lookup(data)
        if (a == None):
            print("data not found")
            return False
        elif (b == None and a!= None):
            if a.left == None and a.right == None:
                a.data = None

            if a.left == None and a.right != None:
                a = a.right
            if a.left != None and a.right == None:
                a = a.left
            if a.left != None and a.right!=None:
                c = a.right
                t = c
                while(c.left!=None and c.right!=None):
                    t = c
                    c = c.left
                t.left = None
                a.data = c.data
            return True
        else:
            if a.data < b.data:
                loc = 0
            elif a.data > b.data:
                loc = 1
            
            if a.left == None and a.right == None:
                if loc == 0:
                    b.left = None
                elif loc == 1:
                    b.right = None
            if a.left == None and a.right != None:
                if loc == 0:
                    b.left = a.right
                elif loc == 1:
                    b.right = a.right
            if a.left != None and a.right == None:
                if loc == 0:
                    b.left = a.left
                elif loc == 1:
                    b.right = a.left
            if a.left != None and a.right!=None:
                c = a.right
                while(c.left!=None and c.right!=None):
                    temp = c
                    c = c.left
                temp.left = None
                a.data = c.data
            return True

                
root = Node(8)
root.insert(9)
root.insert(7)
root.insert(3)
root.insert(5)
a,b = root.lookup(3)
print(a,b)
root.delete(3)
a,b = root.lookup(3)
print(a,b)
a,b = root.lookup(8)
print(a,b)
root.delete(8)
a,b = root.lookup(8)
print(a,b)
```

```
Output:
<__main__.Node object at 0x7fa894f5e610> <__main__.Node object at 0x7fa894f5e550>
None None
<__main__.Node object at 0x7fa894f5e5d0> None
None None
```

### 