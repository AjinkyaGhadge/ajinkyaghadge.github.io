---
title: Notes on Trees
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
# Binary Tree

## Terminology

* **Root** is the topmost node of the tree

* **Edge** is the link between two nodes

* **Child** is a node that has a parent node

* **Parent** is a node that has an edge to a child node

* **Leaf** is a node that does not have a child node in the tree

* **Height** is the length of the longest path to a leaf

* **Depth** is the length of the path to its root


# Binary Search Tree

> Binary search tree is also called an ordered binary tree.

## Properties:
* The left subtree of a node contains only nodes with keys less than the node’s key.
* The right subtree of a node contains only nodes with keys greater than the node’s key.
* Both the left and right subtrees must also be binary search trees.


## Implementation:

### Node Class:

Although a tree can be made to be doubly linked, that is both parent and children have connection to each other, here I have chosen a simple one way connected tree.

Other than its value, a single Node has a left child and a right child. 

```
class Node:
    def __init__(self,data,left=None,right=None):
        self.left = left
        self.right = right
        self.data = data
```

### Implementation

#### Insert Method

Inserting into Binary Tree can be done by using recursion. 

1. Check if number > or < or = to the value at a node (starting at root)
2. If < then apply 1 recursively on left sub-tree, till you find an optimal place as a leaf
3. If > then apply 1 recursively on right sub-tree, till you find an optimal point in the right sub-tree

```
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
```
#### Lookup Method

In lookup, find the parent and the node corresponding to a number. The logic is similar to insertion. Recursively check the left and the right sub-trees

```
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
```

Deletion is a slightly different beast. There are following cases that need handling:

1. Delete a leaf node
2. Delete a node with only one child
3. Delete a node with two children 
4. Delete the root node

Deleting the root node is a special case of 3.

Look at the comments to find how each of the following were done.

```
    def delete(self,data):
        a , b = self.lookup(data)
        if (a == None):
            print("data not found")
            return False
        elif (b == None and a!= None): # 
            if a.left == None and a.right == None: #1. Delete a leaf node
                a.data = None

            if a.left == None and a.right != None: #2. Delete a node with only one child
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
            if a.data < b.data: # 3. Delete a node with two children 
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
            if a.left != None and a.right!=None: # 4. Delete the root node
                c = a.right
                while(c.left!=None and c.right!=None):
                    temp = c
                    c = c.left
                temp.left = None
                a.data = c.data
            return True
```

### Traversals

#### Inorder Traversal

> Inorder traversal, the order is left ---> Node ---> Right

```
def inorder(self):
        if self.left is not None:
            self.left.inorder()
        print(self.data, end = ' ')
        
        if self.right is not None:
            self.right.inorder()
```

#### Preorder Traversal

> Inorder traversal, the order is Node ---> Left ---> Right

```
    def preorder(self):
        print(self.data, end = ' ')
       
        if self.left is not None:
            self.left.preorder()
        if self.right is not None:
            self.right.preorder()
```

#### Postorder Traversal

> Inorder traversal, the order is Right ---> Node ---> left

```
    def postorder(self):
        if self.right is not None:
            self.right.postorder()
        print(self.data, end = ' ')
        
        if self.left is not None:
            self.left.postorder()
```

#### Driver Code

```
root = Node(8)
root.insert(9)
root.insert(7)
root.insert(3)
root.insert(5)
print("Inorder")
root.inorder()
print("Preorder")
root.preorder()
print("Preorder")
root.postorder()
```

### Output

```
Inorder
3 5 7 8 9 
Preorder 
8 7 3 5 9
Preorder
9 8 7 5 3
```

Credits:
* [Laurent Luce's Blog](http://www.laurentluce.com/posts/binary-search-tree-library-in-python/)
* [Data Structures and Algorithms in Python <sub> M H. Goldwasser, R Tamassia, M T. Goodrich </sub>](https://www.oreilly.com/library/view/data-structures-and/9781118290279/)
* [BacktoBack SWE: Binary Tree Bootcamp: Full, Complete, & Perfect Trees. Preorder, Inorder, & Postorder Traversal](https://www.youtube.com/watch?v=BHB0B1jFKQc&t=206s)