---
title: "2016-S3 Phonomenal Reviews"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2016-S3 Phonomenal Reviews

{{< hint info >}}
**Problem Description**  
[View Problem](https://cemc.uwaterloo.ca/contests/computing/2016/stage%201/seniorEn.pdf)
{{< /hint >}}

## Key Insight #1

At first glance, this question may seem like a minimum spanning tree problem. However, the problem description asks us to find an actual path that touches all of the Pho restaurants on the graph. We can break the problem into three big sections.

1. Find the right node to start on
2. Process the graph and find a path
3. Decide on a node to end on

## Key Insight #2

We do not need to find all of the possible optimal starting nodes; we only need to find one. We can find one of the optimal starting nodes by picking any random Pho restaurant and finding the Pho restaurant that is farthest away. This node is an optimal starting node. You can visualize this better by viewing it on a graph. We will use the graph from the second sample input in the problem description to show how this works.

![](/img/phonomenalreviews.png)

In this image, the Pho restaurants are marked in red. If we start at node 0 or node 6, the farthest Pho restaurant is 4 or 7. If we start from either 4 or 7, we will achieve an optimal path. If we pick 3, 4, or 7, the farthest node will be 6 which will give us an optimal path as well.

## Key Insight #3

We can process the graph using a few simple steps. We start at the starting node that we picked, and we look at all of the nodes that are connected. If there is a Pho restaurant in the subtree formed by one of those connecting nodes, we must add two to total cost because we have to travel to that node and back again. We then process all of the node's connections looking for Pho restaurants. This can be easily implemented using a recursive function.

## Key Insight #4

We can choose to start and end on any node on the graph, and we do not have to start and end on the same node. To find the optimal ending node we find the Pho restaurant that is farthest from the starting node. We then subtract the distance from the starting node to the ending node from the distance that we calculated above. This is our final answer.
Optimizations

1. When finding the optimal starting node. We can traverse the graph once using DFS or BFS and store the distance to all nodes on the graph. This is the fastest way since there can be 100,000 nodes, meaning 100,000 calls to our distance function.
2. When processing the graph, we should implement one function that traverses the graph and adds up the cost and another function that just tells us if there is a Pho restaurant in the subtree. This function should use Dynamic Programming to speed up the process.

{{< hint warning >}}
**Please attempt to solve the problem on your own before viewing the solution!**  
[View Solution]({{< relref "solution" >}})
{{< /hint >}}
