---
title: "2015-S4 Convex Hull - Solution"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2015-S4 Convex Hull - Solution

## Input

{{< highlight cpp >}}
//Input
 
int hullThickness, numberOfIslands, numberOfSeaRoutes;
int startingIsland, endingIsland;
 
struct Edge
{
    int destination, time, hullWear;
};
 
struct Node
{
    vector<Edge> edges;
};
 
Node nodes[2001];
 
void addConnection(int a, int b, int t, int h)
{
    Edge edge = Edge();
    edge.destination = b;
    edge.time = t;
    edge.hullWear = h;
    nodes[a].edges.push_back(edge);
}
 
void readInput()
{
    cin >> hullThickness >> numberOfIslands >> numberOfSeaRoutes;
    for (int i = 0; i < numberOfSeaRoutes; i++)
    {
        int a, b, t, h;
        cin >> a >> b >> t >> h;
        addConnection(a, b, t, h);
        addConnection(b, a, t, h);
    }
    cin >> startingIsland >> endingIsland;
}
{{< /highlight >}}

## Solver Variables and Helper Functions

{{< highlight cpp >}}
typedef pair<int, int> pii;
 
unsigned int distanceWithHullBurn[2001][201];
bool nodeInQueue[2001];
 
priority_queue<pii, vector<pii>, greater<pii> > nodeQueue;
 
void setDistance(int node, int hullBurn, unsigned int dist)
{
    distanceWithHullBurn[node][hullBurn] = dist;
}
 
unsigned int getDistance(int node, int hullBurn)
{
    return distanceWithHullBurn[node][hullBurn];
}
 
void setIsNodeInQueue(int node, bool inQueue)
{
    nodeInQueue[node] = inQueue;
}
 
bool isNodeInQueue(int node)
{
    return nodeInQueue[node];
}
{{< /highlight >}}

## Process Edge

{{< highlight cpp >}}
void processEdge(int currentNode, Edge currentEdge)
{
    for (int hullBurn = 0; hullBurn < hullThickness - currentEdge.hullWear; hullBurn++)
    {
        int alternateDistance = getDistance(currentNode, hullBurn) + currentEdge.time;
 
        if (alternateDistance < getDistance(currentEdge.destination, currentEdge.hullWear + hullBurn))
        {
            setDistance(currentEdge.destination, currentEdge.hullWear + hullBurn, alternateDistance);
 
            if (!isNodeInQueue(currentEdge.destination))
            {
                setIsNodeInQueue(currentEdge.destination, true);
                nodeQueue.push(make_pair(alternateDistance, currentEdge.destination));
            }
        }
    }
}
{{< /highlight >}}

## Solver

{{< highlight cpp >}}
void solve()
{
    memset(distanceWithHullBurn, 0x3F, sizeof(distanceWithHullBurn));
    memset(nodeInQueue, false, sizeof(nodeInQueue));
 
    nodeQueue.push(make_pair(0, startingIsland));
 
    setDistance(startingIsland, 0, 0);
    setIsNodeInQueue(startingIsland, true);
 
    while (!nodeQueue.empty())
    {
        int currentNode = nodeQueue.top().second;
        setIsNodeInQueue(currentNode, false);
        nodeQueue.pop();
 
        for (Edge currentEdge : nodes[currentNode].edges)
        {
            processEdge(currentNode, currentEdge);
        }
    }
}
{{< /highlight >}}

## Main Method

{{< highlight cpp >}}
int main(int argc, char* argv[])
{
    readInput();
    solve();
 
    int fastestRoute = 0x3F3F3F3F;
 
    for (int j = 0; j < hullThickness; j++)
    {
        fastestRoute = min(fastestRoute, (int)getDistance(endingIsland, j));
    }
 
    if (fastestRoute == 0x3F3F3F3F)
    {
        cout << -1 << endl;
    }
    else
    {
        cout << fastestRoute << endl;
    }
 
    return 0;
}
{{< /highlight >}}
