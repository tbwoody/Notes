
Following are common types of binary trees.

- **Full Binary Tree**    
- **Complete Binary Tree**    
- **Perfect Binary Tree**    
- **Balanced Binary Tree**    
- **A degenerate (or pathological) Tree**    


## Full Binary Tree

A Binary Tree is full if every node has 0 or 2 children. Following are examples of a full binary tree. We can also say a full binary tree is a binary tree in which all nodes except leaves have two children.

![FullBinaryTree](/images/fullBinaryTree.png)

In a Full Binary, number of leaf nodes is number of internal nodes plus 1    
       L = I + 1    
Where L = Number of leaf nodes, I = Number of internal nodes    

## Complete Binary Tree

A Binary Tree is complete Binary Tree if all levels are completely filled except possibly the last level and the last level has all keys as left as possible    

Following are examples of Complete Binary Trees    
![CompleteBinaryTree](/images/completeBinaryTree.png)

Practical example of Complete Binary Tree is [Binary Heap](https://www.geeksforgeeks.org/binary-heap/)

## Perfect Binary Tree

A Binary tree is Perfect Binary Tree in which all internal nodes have two children and all leaves are at the same level.    
Following are examples of Perfect Binary Trees.    
![PerfectBinaryTree](/images/perfectBinaryTree.png)    

A Perfect Binary Tree of height h (where height is the number of nodes on the path from the root to leaf) has 2h – 1 node.    

Example of a Perfect binary tree is ancestors in the family. Keep a person at root, parents as children, parents of parents as their children.    

## Balanced Binary Tree

A binary tree is balanced if the height of the tree is O(Log n) where n is the number of nodes. For Example, AVL tree maintains O(Log n) height by making sure that the difference between heights of left and right subtrees is 1. Red-Black trees maintain O(Log n) height by making sure that the number of Black nodes on every root to leaf paths are same and there are no adjacent red nodes. Balanced Binary Search trees are performance wise good as they provide O(log n) time for search, insert and delete.    

A binary tree where no leaf is much farther away from the root than any other leaf. Different balancing schemes allow different definitions of “much farther” and different amounts of work to keep them balanced.    
Consider a height-balancing scheme where following conditions should be checked to determine if a binary tree is balanced.    
An empty tree is height-balanced. A non-empty binary tree T is balanced if:    
1) Left subtree of T is balanced    
2) Right subtree of T is balanced    
3) The difference between heights of left subtree and right subtree is not more than 1.    

The above height-balancing scheme is used in [AVL](https://www.geeksforgeeks.org/avl-tree-set-1-insertion/) trees. The diagram below shows two trees, one of them is height-balanced and other is not. The second tree is not height-balanced because height of left subtree is 2 more than height of right subtree.    

![exampleBalancedTree](/images/exampleBalancedTree.png)

## A degenerate (or pathological) Tree
A Tree where every internal node has one child. Such trees are performance-wise same as linked list.  
![oneDegenerateTree](/images/oneDegenerateTree.png)


This artical is original from [internet](https://www.geeksforgeeks.org/binary-tree-set-3-types-of-binary-tree/)
