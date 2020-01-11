---
title: BFS and DFS in graph
subtitle: Implementing depth and breadth first search for a graph
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
  - Graph
  - BFS
  - DFS
  - Graph Traversal
  - Adjacency List
published: true
---
# Graph data structure

Graph contains nodes connected to one another, there isn't a structure to connections. 

## Terminology 

1. Vertex (Node)
2. Edge (a connection between two nodes, can be weighted)
3. Weight
4. Path (a way of traversing from node a to node b)
5. Cycle (a self connecting path for a node: a->b->c->d->a)

## Implementing Graph

### Adjacency Matrix

In a matrix, each entry in x and y axis respectively corresponds to a node. If a edge exists between two nodes, we write the weight at the position (x,y). 

### Adjacency List

Since adjacency matrix can be sparse, and hence space-wise inefficient. Better, use dictionary of dictionaries.

Defining class for vertex:

Vertex Class: 

Each node contains an Id and a dictionary of each connection and corresponding edge weight

```
class Vertex:
    def __init__(self,key):
        self.id = key
        self.connectedTo = {}
    
    def addNeighbor(self,nbr,weight=0):
        self.connectedTo[nbr] = weight

    def __str__(self):
        return str(self.id) + 'connected To ' + str([x.id for x in self.connectedTo])
    
    def getConnections(self):
        return self.connectedTo.keys()

    def getId(self):
        return self.id
    
    def getWeight(self,nbr):
        return self.connectedTo[nbr]
```

Graph Class:

Maitain a list of vertices in the Graph, since each Vertex maintains information about edges

```
class Graph:

    def __init__(self):
        self.vertList = {}
        self.numVertices = 0
    
    def addVertex(self,key):
        self.numVertices = self.numVertices+1
        newVertex = Vertex(key)
        self.vertList[key] = newVertex
        return newVertex

    def getVertex(self,n):
        if n in self.vertList:
            return self.vertList[n]
        else:
            return None
    
    def __contains__(self,n):
        return n in self.vertList
    
    def addEdge(self,f,t,weight=0):
        if f not in self.vertList:
            nv = self.addVertex(f)
        if t not in self.vertList:
            nv = self.addVertex(t)
        self.vertList[f].addNeighbor(self.vertList[t],weight)

    def getVertices(self):
        return self.vertList.keys()
    
    def __iter__(self):
        return iter(self.vertList.values())

```

Depth-First Search:

```
def dfs(self,start,visited = None):
        if visited == None:
            visited = set()
        visited.add(start)
        vert = self.getVertex(start)
        tempset = vert.getConnections()
        l = set()
        for i in tempset:
            l.add(i.getId())
        for nex in l-visited:
            self.dfs(nex,visited)
        return visited
```

Breadth-First Search:

Use FIFO, 
1. for better performance use deque instead of list
2. use set to maintain visited nodes


```
def bfs(self,start):
        q = collections.deque()
        q.append(start)
        visited = set()
        k = list()
        while(q):
            x = q.popleft()
            k.append(x)
            visited.add(x)
            vert = self.getVertex(x)
            tempset = vert.getConnections()
            for i in tempset:
                if i.getId() not in visited:
                    q.append(i.getId())
        return(k)
```

Driver Code:

```
g = Graph()
for i in range(6):
    g.addVertex(i)
g.vertList
g.addEdge(0,1,5)
g.addEdge(0,7,2)
g.addEdge(1,2,2)
g.addEdge(1,9,2)
g.addEdge(2,3,4)
g.addEdge(3,5,9)
g.addEdge(5,0,7)

for v in g:
    for w in v.getConnections():
        print("( %s , %s )" % (v.getId(), w.getId()))

print(g.dfs(0))
print(g.bfs(0))
```
Output:

```
( 0 , 1 )
( 0 , 7 )
( 1 , 2 )
( 1 , 9 )
( 2 , 3 )
( 3 , 5 )
( 5 , 0 )
{0, 1, 2, 3, 5, 7, 9}
[0, 1, 7, 2, 9, 3, 5]
```


# Credits
1. [Adjacency List](https://runestone.academy/runestone/books/published/pythonds/Graphs/AnAdjacencyList.html)
2. [Depth-First Search and Breadth-First Search in Python](https://eddmann.com/posts/depth-first-search-and-breadth-first-search-in-python/)
3. [Deque](https://docs.python.org/2/library/collections.html#collections.deque)