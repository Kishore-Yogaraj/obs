### Binary Tree Definitions
Similar to linked lists, binary trees are another data structure that also involves nodes and pointers. In a linked list we connect nodes in a straight line using `next` and `prev` pointers. Nodes in a binary tree also have at most two pointers, but we call them **left child** and **right child** pointers.

The first node in a binary tree is the **root node** and we draw the pointers down instead of in a straight line. The value of each node can also be any data type

Below is the code for a tree node:
```python
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None
```

**If a node does not have any children, it is called a leaf node**

Binary trees can only point in one direction, unlike linked lists. This means that a leaf node is always guaranteed to exist.

### Properties of leaf nodes

#### Root Node
The root node is the highest node in the tree and has no parent node. All of the nodes in the tree can be reached by the root node

#### Leaf nodes
Leaf nodes are nodes with no children. The nodes at the last level of the tree are guaranteed to be leaf nodes but they can also be found on other levels

#### Height
The height of a binary tree is measured from the root node all the way to lowest leaf node which is similar to height of anything in real life

As shown in the image below, the height of the tree is 3

![[Pasted image 20250125161138.png]]

Sometimes the height of a tree can also be defined by the number of edges that are in between the nodes instead of using the node itself. In this case the height of the tree above would be 2. An easy way to calculate the heigh using edges is by doing `n-1` where `n` is the number of nodes in the tree.

#### Depth
Depth of a binary tree node is measured from itself all the way up to the root. To measure the depth, you simply look at the node you want to find the depth for and then count how many nodes are above it and add 1 to include the node itself

![[Pasted image 20250125161431.png]]

#### Ancestor
A node connected to all nodes below it is considered an ancestor to those nodes. In the example below, the root node would be the ancestor to all of the nodes in the tree.

#### Descendent
The descendent of a node is the child of a node or the child of a child of a node
![[Pasted image 20250125161644.png]]

### Binary Trees vs Binary Search Trees
Binary search tress are a variation of binary trees with the addition of a sorted property where all values smaller than the parent node will be on the left subtree and all values greater than the parent node will be on the right of the subtree.

This allows us to eliminate half of the tree every time we search for a specific element.

This a recursive property which means that it applies to every node in the tree. BSTs are preferred over sorted arrays because they allow for insertion and deletion in O(logn) time. Sorted arrays require O(n) time for these operations.

#### BST search
Since tress area recursive data structure, the simplest way to travers them is by using recursion.
![[Pasted image 20250125163115.png]]

In the above example we would notice that the target is greater than the root node so we would go right and check that node. We see that the node is the target value and so we return that value as `True` saying we reached the target. Otherwise we would return `False`

![[Pasted image 20250125163251.png]]

```python
def search(root, target):
	#Check to see if a node exists. If null (non existent) return false
    if not root: 
        return False

	#Check if target is greater than current node
    if target > root.val:
	    # If it is then go to line 2 and check if a node exists
        return search(root.right, target)
	#Check if target is less than current node
    elif target < root.val:
	    #Go to line 2 and check to see if node exists
        return search(root.left, target)
    else:
        return True # Return true if target = node
```

#### Time Complexity for BST
If the binary tree is balanced, the algorithm will run in O(logn) time. **Balanced** means that the height of the left subtree is equal to the height of the right subtree or there is a difference of 1.

In a balanced tree, we effectively remove half of the nodes through each iteration which make it O(logn) time complexity.

Our worst case scenario is where we have a skewed tree in which the height of the tree is equal to the number of nodes which effectively just makes the binary search tree a sorted array and therefore makes the time complexity O(n)

### BST Insert and Remove
One of the main benefits of using BSTs over a sorted array is that we can perform removal and insertion in O(logn) time instead of O(n) time. This is with the assumption that the tree is balanced

#### Insert
To insert a new node into the BST we need to first traverse the BST to find the right position and then insert this node and we also need to make sure we maintain the sorted property of the BST.

Below is the code to insert a node into a BST
```python
# Insert a new node and return the root of the BST.
def insert(root, val):
    if not root:
        return TreeNode(val)
    
    if val > root.val:
        root.right = insert(root.right, val)
    elif val < root.val:
        root.left = insert(root.left, val)
    return root
```

![[Pasted image 20250125211613.png]]

#### Remove
Before we remove a node from BST, we need to consider two cases:
- The target node has 0 or 1 child
- The target node has 2 children
#### Case 1.1 - Target node has no children
How do we remove the node that contains the value 2:

![[Pasted image 20250125212424.png]]

We start at the parent node. Then check to see which direction to go by using the if statements we've been using for our search and our insert operations.

Then we simply remove the node from the BST: [https://youtu.be/0Pss8Yxf2cg](https://youtu.be/0Pss8Yxf2cg)

If we wish to remove node with value 2 from the BST, then we simply make the left child of the node with value 3 point to null

#### Case 1.2 - Target has one child 
How do we remove the node with value 3 from this BST
![[Pasted image 20250125212950.png]]

You would make the left child pointer of the root node point to the node with value 2 instead of 3
![[Pasted image 20250125213029.png]]

#### Case 2 - Target node has two children
How would we remove the node with value 6 shown below
![[Pasted image 20250125213214.png]]

To delete 6, we replace the node with its **in-order successor**.

The in-order successor is the left most node in the right subtree of the target node. To put it simply, it's the smallest node among all the nodes that are greater than the target node.

After identifying the **in-order successor**, we then replace the deleted node with the in order successor. This means we point the right child of the root node to the in order successor and point the left child of the in order successor to the left child of the target node

```python
# Return the minimum value node of the BST.
def minValueNode(root):
    curr = root
    while curr and curr.left:
        curr = curr.left
    return curr

# Remove a node and return the root of the BST.
def remove(root, val):
    if not root:
        return None
    
    if val > root.val:
        root.right = remove(root.right, val)
    elif val < root.val:
        root.left = remove(root.left, val)
    else:
        if not root.left:
            return root.right
        elif not root.right:
            return root.left
        else:
            minNode = minValueNode(root.right)
            root.val = minNode.val
            root.right = remove(root.right, minNode.val)
    return root
```

#### Time complexity of BST insert and remove
The time complexity of inserting and removing from a BST is proportional to the height of the tree. This is because we are essentially running a binary search on the tree until we reach the target position to insert into, or the target node to remove.

The time complexity of these operations is O(log n)O(log n) if the tree is balanced. However, if the tree is unbalanced, the time complexity can be O(n)O(n) in the worst case.
### Depth First Search
Most common algorithm used in coding interviews and is a way to traverse a tree and search for nodes/values.

The idea is we pick a direction, say left, and keep following pointers as far down left as we can go until we reach null. Once we reach null, we backtrack to the parent node and then go right. We keep doing this until we have visited every node in the tree. This is the essence of depth-first search.

Three way to traverse a tree using DFS
- Inorder
- Preorder
- Postorder

#### Inorder
![[Pasted image 20250126213729.png]]

Code for in order traversal
```python
def inorder(root):
    if not root:
        return    
    inorder(root.left)
    print(root.val)
    inorder(root.right)
```

The order in which these nodes will be visited is `[2,3,4,5,6,7]`, which is sorted. It is important to note that an inorder traversal will only print the nodes in a sorted order if the tree is a binary search tree.

![[Pasted image 20250126214348.png]]
#### Pre order traversal
Pre order traversal will visit the parent node first then visit the left subtree then the right subtree.

When we say **visit** it refers to an action you would perform on this node. In the case of the code below, this would be to print the value of the node:

```python
def preorder(root):
    if not root:
        return    
    print(root.val)
    preorder(root.left)
    preorder(root.right)
```

The nodes are visited in the following order `[4,3,2,6,5,7]`
![[Pasted image 20250126214300.png]]
#### Postorder traversal

In post order the traversal will visit the left subtree, then the right subtree and then the parent node last

```python
def postorder(root):
    if not root:
        return    
    postorder(root.left)
    postorder(root.right)
    print(root.val)
```

The order in which these nodes will be visited is: `[2,3,5,7,6,4]`

![[Pasted image 20250126214243.png]]

#### Depth first search time complexity
We know that we have to visit every node in the tree, and if there are n nodes in the tree, the algorithm will run in O(n), regardless of the height of the tree.

Another interesting point is that we could actually build a sorted array from the inorder traversal of a binary search tree. This is because the nodes are visited in sorted order.