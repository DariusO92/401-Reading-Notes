# Day 14

## Implementation: Trees

### Common Terms

- Node - A Tree node is a component which may contain its own values, and references to other nodes
- Root - The root is the node at the beginning of the tree
- K - A number that specifies the maximum number of children any node may have in a k-ary tree. In a binary tree, k = 2.
- Left - A reference to one child node, in a binary tree
- Right - A reference to the other child node, in a binary tree
- Edge - The edge in a tree is the link between a parent and child node
- Leaf - A leaf is a node that does not have any children
- Height - The height of a tree is the number of edges from the root to the furthest leaf

### Traversals

- An important aspect of trees is how to traverse them. Traversing a tree allows us to search for a node, print out the contents of a tree, and much more! 
- There are two categories of traversals when it comes to trees: Depth First Breadth First

#### Depth First   

- Depth first traversal is where we prioritize going through the depth (height) of the tree first. There are multiple ways to carry out depth first traversal, and each method changes the order in which we search/print the root. Here are three methods for depth first traversal:

Pre-order: root >> left >> right
In-order: left >> root >> right
Post-order: left >> right >> root

- Pre-order
  Let’s break down the pre-order traversal. Here is the pseudocode for this traversal method:

ALGORITHM preOrder(root)

OUTPUT <-- root.value

if root.left is not NULL
preOrder(root.left)

if root.right is not NULL
preOrder(root.right)

Pre-order means that the root has to be looked at first. In our case, looking at the root just means that we output its value. 
When we call preOrder for the first time, the root will be added to the call stack:

### Big O

- The Big O time complexity for inserting a new node is O(n). Searching for a specific node will also be O(n). Because of the lack of organizational structure in a Binary Tree, the worst case for most operations will involve traversing the entire tree. 
  If we assume that a tree has n nodes, then in the worst case we will have to look at n items, hence the O(n) complexity.

- The Big O space complexity for a node insertion using breadth first insertion will be O(w), where w is the largest width of the tree. For example, in the above tree, w is 4.

- A “perfect” binary tree is one where every non-leaf node has exactly two children. The maximum width for a perfect binary tree, is 2^(h-1), where h is the height of the tree. Height can be calculated as log n, where n is the number of nodes.

### Binary Search Tree

- A Binary Search Tree (BST) is a type of tree that does have some structure attached to it. In a BST, nodes are organized in a manner where all values that are smaller than the root are placed to the left, and all values that are larger than the root are placed to the right.