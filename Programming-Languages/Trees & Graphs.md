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
We have two key operations on


##Max-Heaps
Max-Heaps are essentially equivalent with the Min-Heaps, buth the elements are in descending order rather than the ascending order.    

#Tries (Prefix Trees)

#Graphs

##Adjacency List

##Adjacency Matrices

#Graph Search

##Depth-First Search

##Bread-First Search
