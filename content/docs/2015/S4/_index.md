---
title: "2015-S4 Convex Hull"
---

# 2015-S4 Convex Hull

{{< hint info >}}
**Problem Description**  
[View Problem](https://cemc.uwaterloo.ca/contests/computing/past_ccc_contests/2015/stage%201/seniorEn.pdf)
{{< /hint >}}

## Key Insight

We can use a modified dijkstra's algorithm to solve this problem. A normal dijkstra's algorithm uses a distance array to maintain the shortest distance to each node on the graph. Ours will use a distance array that maintains the shortest distance to each node on the graph costing us a certain amount of hull wear.

## Algorithm
1. Create a priority queue.
2. Add the starting island to the queue with a priority of zero.
3. Loop while priority queue is not empty:
	1. Pop the top node off the queue.
	2. Iterate through all of that nodes connections or edges:
		1. Loop through each possible amount of hull burn:
			1. Update the distance to the destination of the current edge if the distance with the current hull burn is shorter than the value stored in our 2D distance array.
			2. When we update the distance we check to see if the node is in the priority queue if it is not then we add it to the queue with the distance we calculated as the priority.
4. Loop through all calculated distances for the ending island and find the lowest one where the hull burn is small enough.
5. Print that number or -1 if there is no path that does not destroy the hull.

{{< hint warning >}}
**Please attempt to solve the problem on your own before viewing the solution!**  
[View Solution]({{< relref "solution" >}})
{{< /hint >}}
