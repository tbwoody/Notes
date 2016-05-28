#Types of Trees
A nice way to undrstand a tree is with a recursive explanation. A tree is a data structure composed of nodes.
- Each tree has a root node.    
- The root node has zero or more child nodes.   
- Each child node has zero or more child nodes, and so on.

The tree can not contain cycles. The nodes may or may not be in a particular order, they could has any data type as values, and they may or may not have
links back to their parent nodes. A very simple class definition for Node is:     
```
Class Node{
  public String name;
  public Node[] children;
}
```
Sometimes we use a Tree class to wrap this node.           
```
class Tree{
  public Node root;
}
```


##Trees vs. Binary Trees       
A binary tree is a tree in which each node has up to two children. A node is called a "leaf" if it has no children.

##Binary Tree vs. Binary Search Tree
A binary search tree is a binary tree in which every node fits a specific ordering property：all left descendents <= n < all right descendents. This must be true for each node n.
Note that this inequality must be true for all of a node's descendents, not just its immediate children.

##Balanced vs. Unbalanced
Two common types of balanced trees are red-black trees and AVL trees.

Red-black trees (a type of self-balancing binary search tree) do not ensure quite as strict balancing, but the nalancing is still good enough to ensure O(logN)
insertions, deletions, and retrievals.They require a bit less memory and can rebalance faster (which means faster insertions and removals), so they are often used in
situations where the tree will be modified frequently.

Red-black trees operate by enforcing a quasi-alternating red and black coloring and then requiring every path from a node to its leaves to have
the same number of black nodes. Doing so leads to a reasonably balanced tree.

**Properties**
- 1. Every node is either red or black.
- 2. The root is black.
- 3. The leaves, which are NULL nodes, are considered black.
- 4. Every red node must have two black children. That is, a red node cannot have red childern.
- 5. Every path from a node to its leaves must have the same number of black children.


##Complete Binary Trees
A complete binary tree is a binary tree in which every level of the tree is fully filled, expept for the last level. To the extent that the last
level is filled, it is filled left to right.

##Full Binary Trees
A full binary tree is a binary tree in which every node has either zero or two children. That is, no nodes have only one child.

##Perfect Binary Trees
A perfect binary tree is one that is both full and complete. All leaf nodes will be at the same level, and this level has the maximum number of nodes.

#Binary Tree Traversal

##In-Order Traversal
In-Order traversal means to "visit" the left branch, then the current node, and finally, the right branch. When performed on a binary search tree,
it visits the nodes in ascending order (hence the name "in-order").
```
void inOrderTraversal(TreeNode node){
  if(node != null){
    inOrderTraversal(node.left);
    visit(node);
    inOrderTraversal(node.right);
  }
}
```
##Pre-Order Traversal
Pre-order traversal vists the current node before its child nodes. In a pre-order traversal, the root is always the first node visited.   
```
void preOrderTraversal(TreeNode node){
  if(node != null){
    visit(node);
    preOrderTraversal(node.left);
    preOrderTraversal(node.right);
  }
}
```

## Post-Order Traversal
Post-order traversal visits the current node after its child nodes. In a post-order traversal, the root node is always the last node visited.  
```
void postOrderTraversal(TreeNode node){
  if(node != null){
    postOrderTraversal(node.left);
    postOrderTraversal(node.right);
    visit(node);
  }
}
```

#Binary Heaps (Min-Heaps and Max-Heaps)
##Min-Heaps
A min-heap is a "complete" binary tree (that is, totall filled other than the rightmost elements on the last level) where each
node is small than its children. The root is the minimum element in the tree.      
We have two key operations on a min-heap: insert and extract_min.

**Insert**   
When we insert into a min-heap, we always start by inserting the element at the bottom. We insert at the rightmost spot so as to maintain the complete tree property. Then we "fix" the tree by swapping the new element with its parent, until we find an appropriate spot for the element. We essentially bubble up the minimum element.      

**Extract Minimum Element**             
Finding the minimum element of a min-heap is easy: it is always at the top. The tricker part is how to remove it.   
First, we remove the minimum element and swap it with the last element in the heap (the bottommost, rightmost element). Then we bubble down this element, swapping it with one of its childrean until the min heap property is restored.       
Do we swap it with the left child or the right child? That depends on their values. There is no inherent ordering between the left and right element, but you will need to take the smaller one in order to maintain the min-heap ordering.



##Max-Heaps
Max-Heaps are essentially equivalent with the Min-Heaps, buth the elements are in descending order rather than the ascending order.    

#Tries (Prefix Trees)
A trie is a variant of an n-ary tree in which characters are stored at each node. Each path down the tree may represent a word.

#Graphs
A tree is actually a type of graph, but not all graphs are trees. Simply put, a tree is a connected graph without cycles.   
A graph is simply a collection of nodes with edges between them.     
- Graphs can be either directed or undirected. While directed edges are like a one-way street, undirected edges are like a two-way street.
- The graph might consist of multiple isolated subgraphs. If there is a path between every pair of vertices, it is called a "connected graph".
- The graph can also have cycles or not. An "acyclic graph" is one without cycles.

##Adjacency List
This is the most common way to represent a graph. Every vertex or node stores a list of adjacent vertices. In an undirected graph, an edge like (a,b) would be stored twice: once in a's adjacent vertices and once in b's adjacent vertices.
##Adjacency Matrices
An adjacency matrix is an NxN boolean matrix (where N is the number of nodes), where a true value at matrix[i][j] indicates an edge from the node i to node j.

#Graph Search
The two most common ways to search a graph are depth-first search and breadth-first search.    

In depth-first search (DFS), we start at the root (or another arbitrarily selected node) and explore each branch completely before movin on to the next branch. That is, we go deep first before we go wide.   

In breadth-first search (BFS), we start at the root (or another arbitrarily selected node) and explore each neighbor before going on to any of their children. That is, we go wide first before we go deep.

##Depth-First Search
```
void DFS(Node root){
  if(root == null){
    return;
  }
  
  visit(root);
  root.visited = true;
  
  for each (Node n in root.adjacent){
    if(n.visited == false){
      DFS(n);
    }
  }
}
```

##Bread-First Search

```
void BFS(Node root){
  if(root == null){
    return;
  }
  
  Queue queue = new Queue();
  root.marked = true;
  queue.enqueue(root);
  
  while(!queue.isEmpty()){
  Node r = queue.dequeue();
  visit(r);
  foreach(Node n in r.adjecent){
    if(n.marked == false){
      n.marked = true;
      queue.enqueue(n);
    }
  }
  }
}
```
