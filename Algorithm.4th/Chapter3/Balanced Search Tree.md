### Balanced Search Tree

#### 2-3 search trees

The primary step to get the flexibility that we need to guarantee balance in search trees is to allow the nodes in our trees to hold more than one key. Specifically, referring to the nodes in a standard BST as 2-nodes (they hold two links and one key), we now also allow 3-nodes, which hold three links and two keys. Both 2-nodes and 3-nodes have one link for each of the intervals subtended by its keys.

##### Definition

A 2-3 search tree is a tree that is either empty or

* A 2-node, with one key (and associated value) and two links, a left link to a 2-3 search tree with smaller keys, and a right link to a 2-3 search tree with larger keys
* A 3-node, with two keys (and associated values) and three links, a left link to a 2-3 search tree with smaller keys, a middle link to a 2-3 search tree with keys between the node’s keys, and a right link to a 2-3 search tree with larger keys

As usual, we refer to a link to an empty tree as a null link.

##### Search

The search algorithm for keys in a 2-3 tree directly generalizes the search al- gorithm for BSTs. To determine whether a key is in the tree, we compare it against the keys at the root. If it is equal to any of them, we have a search hit; otherwise, we follow the link from the root to the subtree corresponding to the interval of key values that could contain the search key. If that link is null, we have a search miss; otherwise we recursively search in that subtree.

##### Insert into a 2-node

To insert a new node in a 2-3 tree, we might do an unsuccessful search and then hook on the node at the bottom, as we did with BSTs, but the new tree would not remain perfectly balanced.

##### Insert into a tree consisting of a single 3-node

As a first warmup before considering the general case, suppose that we want to insert into a tiny 2-3 tree consisting of just a single 3-node. Such a tree has two keys, but no room for a new key in its one node. To be able to perform the insertion, we temporarily put the new key into a 4-node, a natural extension of our node type that has three keys and four links. Creating the 4-node is convenient because it is easy to convert it into a 2-3 tree made up of three 2-nodes, one with the middle key (at the root), one with the smallest of the three keys (pointed to by the left link of the root), and one with the largest of the three keys (pointed to by the right link of the root). Such a tree is a 3-node BST and also a perfectly balanced 2-3 search tree, with all the null links at the same distance from the root.

##### Insert into a 3-node whose parent is a 2-node

As a second warmup, suppose that the search ends at a 3-node at the bottom whose parent is a 2-node. In this case, we can still make room for the new key while maintaining perfect balance in the tree, by making a temporary 4-node as just described, then splitting the 4-node as just described, but then, instead of creating a new node to hold the middle key, moving the middle key to the node’s parent.

##### Insert into a 3-node whose parent is a 3-node

Now suppose that the search ends at a node whose parent is a 3-node. Again, we make a temporary 4-node as just described, then split it and insert its middle key into the parent. The parent was a 3-node, so we replace it with a temporary new 4-node containing the middle key from the 4-node split.

##### Splitting the root

If we have 3-nodes along the whole path from the insertion point to the root, we end up with a temporary 4-node at the root. In this case we can proceed in precisely the same way as for insertion into a tree consisting of a single 3-node. We split the temporary 4-node into three 2-nodes, increasing the height of the tree by 1. Note that this last transformation preserves perfect balance because it is performed at the root.

##### Local transformations

Splitting a temporary 4-node in a 2-3 tree involves one of six transformations, summarized at the bottom of the next page. The 4-node may be the root; it may be the left child or the right child of a 2-node; or it may be the left child, middle child, or right child of a 3-node. The basis of the 2-3 tree insertion algorithm is that all of these transformations are purely local: no part of the tree needs to be examined or modified other than the specified nodes and links. The number of links changed for each transformation is bounded by a small constant.

##### Global properties

Moreover, these local transformations preserve the global properties that the tree is ordered and perfectly balanced: the number of links on the path from the root to any null link is the same. If the length of every path from a root to a null link is h before the transformation, then it is h after the transformation. Each transformation preserves this property, even while splitting the 4-node into two 2-nodes and while changing the parent from a 2-node to a 3-node or from a 3-node into a temporary 4-node. When the root splits into three 2-nodes, the length of every path from the root to a null link increases by 1.

##### Proposition F

Search and insert operations in a 2-3 tree with N keys are guaranteed to visit at most ![](http://latex.codecogs.com/gif.latex?lgN) nodes.

#### Red-black BSTs

The insertion algorithm for 2-3 trees just described is not difficult to understand; now, we will see that it is also not difficult to implement. We will consider a simple representation known as a red-black BST that leads to a natural implementation.

##### Encoding 3-nodes

The basic idea behind red-black BSTs is to encode 2-3 trees by starting with standard BSTs (which are made up of 2-nodes) and adding extra information to encode 3-nodes. We think of the links as being of two different types: red links, which bind together two 2-nodes to represent 3-nodes, and black links, which bind together the 2-3 tree. Specifically, we represent 3-nodes as two 2-nodes connected by a single red link that leans left (one of the 2-nodes is the left child of the other).

An equivalent definition. Another way to proceed is to define red-black BSTs as BSTs having red and black links and satisfying the following three restrictions:

* Red links lean left.
* No node has two red links connected to it.
* The tree has perfect black balance : every path from the root to a null link has the same number of black links.

There is a 1-1 correspondence between red-black BSTs defined in this way and 2-3 trees.

##### A 1-1 correspondence

If we draw the red links horizontally in a red-black BST, all of the null links are the same distance from the root, and if we then collapse together the nodes connected by red links, the result is a 2-3 tree.

##### Color representation

For convenience, since each node is pointed to by precisely one link (from its parent), we encode the color of links in nodes, by adding a boolean instance variable color to our Node data type, which is true if the link from the parent is red and false if tit is black. By convention, null links are black. For clarity in our code, we define constants RED and BLACK for use in setting and testing this variable.

##### Rotations

The implementation that we will consider might allow right-leaning red links or two red links in a row during an operation, but it always corrects these conditions before completion, through judicious use of an operation called rotation that switches the orientation of red links.

##### Resetting the link in the parent after a rotation

Whether left or right, every rotation leaves us with a link. We always use the link returned by rotateRight() or rotateLeft() to reset the appropriate link in the parent (or the root of the tree).

##### Insert into a single 2-node

A red-black BST with 1 key is just a single 2-node. Inserting the second key immediately shows the need for having a rotation operation. If the new key is smaller than the key in the tree, we just make a new (red) node with the new key and we are done: we have a red-black BST that is equivalent to a single 3-node. But if the new key is larger than the key in the tree, then attaching a new (red) node gives a right-leaning red link, and the code root = rotateLeft(root); completes the insertion by rotating the red link to the left and updating the tree root link. The result in both cases is the red-black representation of a single 3-node, with two keys, one left-leaning red link, and black height 1.

##### Insert into a 2-node at the bottom

We insert keys into a red-black BST as usual into a BST, adding a new node at the bottom (respecting the order), but always connected to its parent with a red link. If the parent is a 2-node, then the same two cases just discussed are effective. If the new node is attached to the left link, the parent simply becomes a 3-node; if it is attached to a right link, we have a 3-node leaning the wrong way, but a left rotation finishes the job.

##### Insert into a tree with two keys (in a 3-node)

* The simplest of the three cases is when the new key is larger than the two in the tree and is therefore attached on the rightmost link of the 3-node, making a balanced tree with the middle key at the root, connected with red links to nodes containing a smaller and a larger key. If we flip the colors of those two links from red to black, then we have a balanced tree of height 2 with three nodes, exactly what we need to maintain our 1-1 correspondence to 2-3 trees. The other two cases eventually reduce to this case.
* If the new key is smaller than the two keys in the tree and goes on the left link, then we have two red links in a row, both leaning to the left, which we can reduce to the previous case (middle key at the root, connected to the others by two red links) by rotating the top link to the right.
* If the new key goes between the two keys in the tree, we again have two red links in a row, a right-leaning one below a left-leaning one, which we can reduce to the previous case (two red links in a row, to the left) by rotating left the bottom link.

##### Flipping colors

To flip the colors of the two red children of a node, we use a method flipColors(), shown at left. In addition to flipping the colors of the children from red to black, we also flip the color of the parent from black to red.

##### Keeping the root black

In the case just considered (insert into a single 3-node), the color flip will color the root red.

##### Insert into a 3-node at the bottom

Now suppose that we add a new node at the bottom that is connected to a 3-node. The same three cases just discussed arise. Either the new link is connected to the right link of the 3-node (in which case we just flip colors) or to the left link of the 3-node (in which case we need to rotate the top link right and flip colors) or to the middle link of the 3-node (in which case we rotate left the bottom link, then rotate right the top link, then flip colors). Flipping the colors makes the link to the middle node red, which amounts to passing it up to its parent, putting us back in the same situation with respect to the parent, which we can fix by moving up the tree.

##### Passing a red link up the tree

The 2-3 tree insertion algorithm calls for us to split the 3-node, passing the middle key up to be inserted into its parent, continuing until encountering a 2-node or the root.

* If the right child is red and the left child is black, rotate left.
* If both the left child and its left child are red, rotate right.
* If both children are red, flip colors.

####Implementation

Since the balancing operations are to be performed on the way up the tree from the point of insertion, implementing them is easy in our standard recursive implementation: we just do them after the recursive calls, as shown in Algorithm 3.4.

##### Deletion

To describe it, we begin by returning to 2-3 trees. As with insertion, we can define a sequence of local transformations that allow us to delete a node while still maintaining perfect balance. The process is somewhat more complicated than for insertion, because we do the transformations both on the way down the search path, when we introduce temporary 4-nodes (to allow for a node to be deleted), and also on the way up the search path, where we split any leftover 4-nodes (in the same manner as for insertion).

##### Top-down 2-3-4 trees

As a first warmup for deletion, we consider a simpler algorithm that does transformations both on the way down the path and on the way up the path: an insertion algorithm for 2-3-4 trees, where the temporary 4-nodes that we saw in 2-3 trees can persist in the tree. The insertion algorithm is based on doing transformations on the way down the path to maintain the invariant that the current node is not a 4-node (so we are assured that there will be room to insert the new key at the bottom) and transformations on the way up the path to balance any 4-nodes that may have been created. The transformations on the way down are precisely the same transformations that we used for splitting 4-nodes in 2-3 trees.

To implement this algorithm with redblack BSTs, we

* Represent 4-nodes as a balanced subtree of three 2-nodes, with both the left and
right child connected to the parent with a red link
* Split 4-nodes on the way down the tree with color flips
* Balance 4-nodes on the way up the tree with rotations, as for insertion

##### Delete the minimum

As a second warmup for deletion, we consider the operation of deleting the minimum from a 2-3 tree. The basic idea is based on the observation that we can easily delete a key from a 3-node at the bottom of the tree, but not from a 2-node.

On the way down the tree, one of the following cases must hold:
* If the left child of the current node is not a 2-node, there is nothing to do.
* If the left child is a 2-node and its immediate sibling is not a 2-node, move a key from the sibling to the left child.
* If the left child and its immediate sibling are 2-nodes, then combine them with the smallest key in the parent to make a 4-node, changing the parent from a 3-node to a 2-node or from a 4-node to a 3-node.

##### Delete

The same transformations along the search path just described for deleting the minimum are effective to ensure that the current node is not a 2-node during a search for any key. If the search key is at the bottom, we can just remove it. If the key is not at the bottom, then we have to exchange it with its successor as in regular BSTs. Then, since the current node is not a 2-node, we have reduced the problem to deleting the minimum in a subtree whose root is not a 2-node, and we can use the procedure just described for that subtree. After the deletion, as usual, we split any remaining 4-nodes on the search path on the way up the tree.

#### Properties of red-black BSTs

The end result is that all symbol-table operations in red-black BSTs are guaranteed to be logarithmic in the size of the tree (except for range search, which additionally costs time proportional to the number of keys returned).

##### Analysis

First, we establish that red-black BSTs, while not perfectly balanced, are always nearly so, regardless of the order in which the keys are inserted.

##### Proposition G

The height of a red-black BST with N nodes is no more than ![](http://latex.codecogs.com/gif.latex?2lgN).

##### Property H

The average length of a path from the root to a node in a red-black BST with N nodes is ~![](http://latex.codecogs.com/gif.latex?1.00lgN).

##### Ordered symbol-table API

One of the most appealing features of red-black BSTs is that the complicated code is limited to the put() (and deletion) methods. Our code for the minimum/maximum, select, rank, floor, ceiling and range queries in standard BSTs can be used without any change, since it operates on BSTs and has no need to refer to the node color. Algorithm 3.4, together with these methods (and the deletion methods), leads to a complete implementation of our ordered symbol-table API. Moreover, all of the methods benefit from the near-perfect balance in the tree because they all require time proportional to the tree height, at most.

##### Proposition I

In a red- black BST, the following operations take logarithmic time in the worst case: search, insertion, finding the minimum, finding the maximum, floor, ceiling, rank, select, delete the minimum, delete the maximum, delete, and range count.

<table>
    <tr>
        <th rowspan="2">algorithm(data structure)</th>
        <th colspan="2">worst-case cost(after N inserts)</th>
        <th colspan="2">average-case cost(after N random inserts)</th>
        <th rowspan="2">efficiently support ordered operations?</th>
    </tr>
    <tr>
        <th>search</th>
        <th>insert</th>
        <th>search hit</th>
        <th>insert</th>
    </tr>
    <tr>
        <th>sequential search(unordered linked list)</th>
        <th>N</th>
        <th>N</th>
        <th><img src=http://latex.codecogs.com/gif.latex?\frac{N}{2}></img></th>
        <th>N</th>
        <th>No</th>
    </tr>
    <tr>
        <th>binary search(ordered array)</th>
        <th><img src=http://latex.codecogs.com/gif.latex?lgN></img></th>
        <th>2N</th>
        <th><img src=http://latex.codecogs.com/gif.latex?lgN></img></th>
        <th>N</th>
        <th>yes</th>
    </tr>
    <tr>
        <th>binary tree search(BST)</th>
        <th>N</th>
        <th>N</th>
        <th><img src=http://latex.codecogs.com/gif.latex?1.39lgN></img></th>
        <th><img src=http://latex.codecogs.com/gif.latex?1.39lgN></th>
        <th>yes</th>
    </tr>
    <tr>
        <th>binary search(ordered array)</th>
        <th><img src=http://latex.codecogs.com/gif.latex?2lgN></img></th>
        <th><img src=http://latex.codecogs.com/gif.latex?2lgN></img></th>
        <th><img src=http://latex.codecogs.com/gif.latex?1.00lgN></img></th>
        <th><img src=http://latex.codecogs.com/gif.latex?1.00lgN></img></th>
        <th>yes</th>
    </tr>
</table>