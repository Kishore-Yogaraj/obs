### General Knowledge
A* is a common path finding algorithm used in robotics in order to determine a path to go from a start pose to an end pose

But how exactly does this algorithm work?

An important equation to understand before jumping into the A* algorithm is `F = G + H`. These variables get calculated every time we create a new node.
- F is the total cost of the node
- G is the distance between the current node and the start node
- H is the heuristic - estimated distance from the current node to the end node

Here is a graphic to visualize this:
![[Pasted image 20250210121102.png]]
Let's say we start at `node 0` and `node 19` is our end position. Let's also say our current node is at the red square which is `node 4`.

- G is the distance between the current node and the start node
	- If we count backwards not including the current node we can see that the current node is 4 spaces away from our start node
	- We can also say the G is 1 more than the **parent node** (`node 3`). So in this case for `node(4)` , `currentNode.g` = 4
- H is the **heuristic** which is the estimated distance from the current node to the end node
	- If we take a look at the graphic above we can see that if we go over 7 spaces to the right (including the current node) and 3 spaces up (not including the current node) we would reach the end node, `node 19`
	- If we apply the **Pythagorean theorem** to this we will get `currentNode.h = 7^2 + 3^2` or `currentNode.h = 58`. **Node that we don't have to apply the square root to 58**.
	- With a heuristic, we need to make sure that we can actually calculate it
	- Also important to note that the heuristic is always an **underestimation** of the total path because an **overestimation** will lead to A* searching through nodes that may not be the best in terms of an `f` value
- F is the total cost of the node
	- To do this we add out `h` and `g` so we would do `currentNode.g + currentNode.h = currentNode.f` or `4 + 58 = 62`

So what do we do with this `f` value?

With this value we can look at our nodes and ask ourselves "Is this the best node to pick to move forward with right now?". Instead of having to run through every node, we can pick the ones that have the best chance of getting us to our goal

With A* it attempts to find the shortest path to the goal at first and then aims to prioritize finding the node we the lowest `f` and best chance of reaching the end.

### In-depth Understanding
https://www.youtube.com/watch?v=QR3U1dgc5RE&t=24s

Goal is to using a binary occupancy grid for motion planning.

How do we find a trajectory in this environment from the starting point to the end point?

We can use **search based algorithms** like A* and **sampling based algorithms** like RRT and RRT*

If we are working with ground based robot it will most likely have 3 properties of its states which are: 
- `x-location`
- `y-location`
- `orientation`

A path would be a sequence of these states that smoothly connect the start pose and end pose.

The process of finding these poses is called **path planning** which is a larger part of the **motion planning** problem.

With **motion planning** we are also concerned with the derivatives of these poses:
- `velocity`
- `acceleration`
- `rotation`

With motion planning we're trying to figure out how precisely the robot travels through the environment.

For this example we're going to be working with an **omnidirectional robot** which means we are only concerned with the `x and y locations`. 

How do graph based algorithms work?

They work by **discretizing** the environment which means to divide the environment into discrete "cells" or "nodes". The goal would then be to find a path using only these nodes

Let's start with a random attempt to find a path to the goal pose:
![[Pasted image 20250210124259.png]]

Going from node to node we calculate the **cost** it takes to get from the start node to the end node. Cost in this case is the distance from one node to another. The total cost is the sum of these distances. In th above example, the path created has a cost of 10. 

Although we found a path that woks, it may not be the shortest path to the goal point. 

What we can do now is start again from the start node and then create a new path at calculate the cost of that. If we happen to run into a previously used node we can then compare the two paths and pick the path that has the lowest cost as shown below:
![[Pasted image 20250210125441.png]]
If there are two different ways to reach a singular node, you can remove the path that has the greater cost at keep iterating through this until you get to the goal pose. However, doing this randomly isn't efficient which is why we use our **algorithms**

![[Pasted image 20250210125758.png]]


#### Search Based methods
To perform path planning with a search based method we first discretize the graph:
![[Pasted image 20250210125946.png]]

We then check every cell around the starting cell and assign it a cost
- In the below example, cells that are adjacent to the starting cell are distance of 10 and cells that are diagonal to the cell are a cost of 14
![[Pasted image 20250210130101.png]]

We would then iterate through all the cells we have generated paths to and calculate the cost to get to that cell and if there is more than 1 way to get to that cell we use the shorter path and update the cost which is similar to our random search approach:
![[Pasted image 20250210130316.png]]

After searching through every single cell, we determine the path to the goal which is the path with them minimum cost at the goal:
![[Pasted image 20250210130436.png]]

Although this is more methodical than random search and is faster and more efficient than the random method most of the time it can become computationally expensive when the grid becomes larger.

#### A*
The A* algorithm makes the process more efficient by keeping track of the distance from the current node and the goal node and prioritizing nodes that have a lower distance to the goal node. This is called the **heuristic**. A straight line distance between two points.
![[Pasted image 20250210130829.png]]

We first calculate the distance from the start node to the goal node using the Pythagorean theorem and and then we subtract from this depending on which node we travel to. For example if the total distance is 48 units and we travel to the node with a cost of 10, we know there are 38 units left to go:

This means that we should prioritize the node with a cost of 14 because it results in a shorter "distance to go" than the node with a cost of 10:

![[Pasted image 20250210131154.png]]

After we pick a node to prioritize, we then check the nodes and the cost to get to each of the prioritized nodes as well as keeping tracking of the distance to the goal node and continue iterating through the process of: Check cost of all nodes surrounding current node, check distance to go to goal node, prioritize node with shortest distance to go:
![[Pasted image 20250210131430.png]]

We will also run into a scenario where multiple cells will have the same F cost. In this case we check one cell with an F cost and if it provides another cell with a lower F cost we continue on that path. If the cell we checked ends producing cells with a higher F cost, we go check the other cells that had the same F cost as the one we had initially. We continue this iterative process until we reach the end.
#### Sampling based algorithms
A* becomes very computationally expensive when the graph becomes a higher resolution. To solve this we use sampling based algorithms

A common algorithim used for sampling based is RRT (Randomly exploring random trees)

How this works is by placing a random node anywhere in the state space. Once we have this node, we want to connect it to the nearest node in our tree. Since we are just starting, this will be our starting node. 

We don't want to place it too far because the chance for variability is much larger. To prevent this we place a threshold for the max distance:
![[Pasted image 20250210132141.png]]

If obstacles are in between the randomly placed node and the starting node we simply ignore those nodes.

Once a node is placed another random node will be placed and a check will be performed to see what the closest node to that node actually is and connect it. RRT will continue doing this until it reaches a threshold near the goal pose:
![[Pasted image 20250210132525.png]]

The **RRT** algorithm will find a path and determine if a path does exist between the start goal and the end goal. However, this may not always be the shortest path so we can use the **RRT**** algorithm to do this

#### RRT*
The node selection process for RRT* is the same where it selects a random node, then finds the nearest node and if there's no obstacle in the way we place a new node at the random node.

The difference comes from where connect this random node to the existing tree. Instead of connecting to the nearest node, we check the nodes around some specified area (search radius) and determine if there's a way to reconnect these nodes in a way that maintains the tree structure but also minimizes the total path length back to the start node:
![[Pasted image 20250210133009.png]]

![[Pasted image 20250210133146.png]]

Connects to the node that has the shortest path length back to the start node
![[Pasted image 20250210133157.png]]
