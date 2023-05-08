---
title: "2016-CCOQR-Q2 Through A Maze Darkly"
---

# 2016-CCOQR-Q2 Through A Maze Darkly

{{< hint info >}}
**Problem Description**  
[View Problem](https://cemc.uwaterloo.ca/contests/computing/past_ccc_contests/2016/CCOQR/maze.pdf)
{{< /hint >}}

## Key Insight #1

The input data is provided in clockwise order but we are asked to walk through the rooms using our right hand which implies a counter-clockwise rotation. So we need to navigate through the doors provided in reverse order.

## Key Insight #2

The first problem that must be solved is the need to iterate quickly through the rooms. The input data as presented makes this a challenge. Using the input data provided in the example:

| Room# | 1 | 2 | 3 |
|-------|---|---|---|
| 1     | 3 |   |   |
| 2     | 5 | 4 |   |
| 3     | 1 |   |   |
| 4     | 2 | 5 | 6 |
| 5     | 4 | 6 |   |
| 6     | 4 |   |   |

Assuming we start our navigation from room 5 to room 4 the step is easy. The first door connects you to room 4. However, when you arrive at room 4 you need to search all the doors and find the door number 5 (you came in from) and then use the door presented right before (door to room 2 - in green). This search is very expensive because it must be done every time we navigate. To reduce the cost we need to pre-compute the door number that is next for each door:

| Room# | 1     | 2     | 3 |
|-------|-------|-------|---|
| 1     | 3     |       |   |
| 2     | 5     | 4     |   |
| 3     | 1     |       |   |
| 4     | **2** | **5** | 6 |
| **5** | 4     | 6     |   |
| 6     | 4     |       |   |

To do this we need to go through each room and and each door and maintain the reverse lookup. For example when we investigate room 5 door 0 witch takes us to room 4 we need to have room 4 remember this (that a path came from room 5 at door 0). Then we can go through all the rooms again and store the next door information as such:

| Room# | 1         | 2         | 3         |
|-------|-----------|-----------|-----------|
| 1     | 3 **(1)** |           |           |
| 2     | 5 **(1)** | 4 **(3)** |           |
| 3     | 1 **(1)** |           |           |
| 4     | 2 **(1)** | 5 **(2)** | 6 **(1)** |
| 5     | 4 **(1)** | 6 **(2)** |           |
| 6     | 4 **(1)** |           |           |

{{< hint danger >}}
**Brute Force Implementation**

A brute force solution that receives 5/15 points and uses the next door optimization.

[View Brute Force Solution]({{< relref "brute-force" >}})
{{< /hint >}}

## Key Insight #3

As seen from the brute force example above the problem requires walking through the rooms over and over again to compute the path length at each door. This requires too much time. The way to reduce the time required to do this we need to walk the paths as minimally as possible.

Imagine we started in a random room and walked the path until we ended up at the same room and same door two times (you probably entered and exited the starting room several times during this walk). If you were to write down the room numbers for the example problem you would have the following list of rooms (we don't need the start room at the end):

Starting in Room 1 (in bold):

| 0              | 1 | 2              | 3 |
|----------------|---|----------------|---|
| {{<fg red 1>}} | 3 | {{<fg red 1>}} | 3 |

Staring in Room 2 (in red):

| 0              | 1 | 2 | 3 | 4 | 5              | 6 | 7 | 8 | 9 |
|----------------|---|---|---|---|----------------|---|---|---|---|
| {{<fg red 2>}} | 4 | 6 | 4 | 5 | {{<fg red 2>}} | 4 | 6 | 4 | 5 |

If we then scanned this list and computed the biggest distance between entering and leaving a room we would find the following (pink for the start and end room and green for the path in the middle:

{{< rawhtml >}}
<table>
<thead>
	<tr>
		<td><b>0</b></td>
		<td><b>1</b></td>
		<td><b>2</b></td>
		<td><b>3</b></td>
		<td><b>Distance</b></td>
	</tr>
</thead>
<tbody>
	<tr>
		<td class="highlight-red">1</td>
		<td class="highlight-green">3</td>
		<td class="highlight-red">1</td>
		<td>3</td>
		<td>2-0 = 2</td>
	</tr>
	<tr>
		<td>1</td>
		<td class="highlight-red">3</td>
		<td class="highlight-green">1</td>
		<td class="highlight-red">3</td>
		<td>3-1 = 2</td>
	</tr>
</tbody>
</table>
{{< /rawhtml >}}

{{< rawhtml >}}
<table>
<thead>
	<tr>
		<td><b>0</b></td>
		<td><b>1</b></td>
		<td><b>2</b></td>
		<td><b>3</b></td>
		<td><b>4</b></td>
		<td><b>5</b></td>
		<td><b>6</b></td>
		<td><b>7</b></td>
		<td><b>8</b></td>
		<td><b>9</b></td>
		<td><b>Distance</b></td>
	</tr>
</thead>
<tbody>
	<tr>
		<td class="highlight-red">2</td>
		<td class="highlight-green">4</td>
		<td class="highlight-green">6</td>
		<td class="highlight-green">4</td>
		<td class="highlight-green">5</td>
		<td class="highlight-red">2</td>
		<td>4</td>
		<td>6</td>
		<td>4</td>
		<td>5</td>
		<td>5-0 = 5</td>
	</tr>
	<tr>
		<td>2</td>
		<td class="highlight-red">4</td>
		<td class="highlight-green">6</td>
		<td class="highlight-red">4</td>
		<td>5</td>
		<td>2</td>
		<td>4</td>
		<td>6</td>
		<td>4</td>
		<td>5</td>
		<td>3-1 = 2</td>
	</tr>
	<tr>
		<td>2</td>
		<td>4</td>
		<td class="highlight-red">6</td>
		<td class="highlight-green">4</td>
		<td class="highlight-green">5</td>
		<td class="highlight-green">2</td>
		<td class="highlight-green">4</td>
		<td class="highlight-red">6</td>
		<td>4</td>
		<td>5</td>
		<td>7-2 = 5</td>
	</tr>
	<tr>
		<td>2</td>
		<td>4</td>
		<td>6</td>
		<td class="highlight-red">4</td>
		<td class="highlight-green">5</td>
		<td class="highlight-green">2</td>
		<td class="highlight-red">4</td>
		<td>6</td>
		<td>4</td>
		<td>5</td>
		<td>6-3 = 3</td>
	</tr>
	<tr>
		<td>2</td>
		<td>4</td>
		<td>6</td>
		<td>4</td>
		<td class="highlight-red">5</td>
		<td class="highlight-green">2</td>
		<td class="highlight-green">4</td>
		<td class="highlight-green">6</td>
		<td class="highlight-green">4</td>
		<td class="highlight-red">5</td>
		<td>9-4 = 5</td>
	</tr>
	<tr>
		<td>2</td>
		<td>4</td>
		<td>6</td>
		<td>4</td>
		<td>5</td>
		<td>2</td>
		<td class="highlight-red">4</td>
		<td class="highlight-green">6</td>
		<td class="highlight-red">4</td>
		<td>5</td>
		<td>8-6 = 2</td>
	</tr>
</tbody>
</table>
{{< /rawhtml >}}

Given this analysis I can determine that the path lengths for the rooms are:

| Room | Path Lengths | Maximum Path |
|------|--------------|--------------|
| 1    | 2            | 2            |
| 2    | 5            | 5            |
| 3    | 2            | 2            |
| 4    | 3,3,2        | 3            |
| 5    | 5            | 5            |
| 6    | 2,5          | 5            |

This approach requires walking the path only two times as long as we mark every door we enter so that we know that we have computed that path already. The distance computation can be done as we walk and a simple array of starting locations for each room can keep track of what the distance was when we left the room and what the distance was when we returned. We only have to remember the longest path for each room.

Note that compared to the brute force solution which requires walking 38 hallways to compute the room maximums this approach only requires walking 14 hallways. Obviously this reduction becomes substantially larger as the number of rooms increases.

{{< hint warning >}}
**Please attempt to solve the problem on your own before viewing the solution!**  
[View Solution]({{< relref "solution" >}})
{{< /hint >}}
