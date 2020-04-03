---
title: "2016-CCOQR-Q3 Data Structure - Brute Force"
---

# 2016-CCOQR-Q3 Data Structure - Brute Force Solution

## Algorithm

The approach in this implementation is to recursively balance the nodes using dynamic programming to keep track of the state of every node.

## Testing Results

| Subtask # | Result                |
|-----------|-----------------------|
|1          | Correct               |
|2          | Correct               |
|3          | Memory Limit Exceeded |
|4          | Memory Limit Exceeded |

## Implementation

{{< highlight cpp >}}
#include <cstring>
#include <iostream>
 
using namespace std;
 
int* dp;
long long n, m;
long long cost;
 
void stabilizePyramid(long long x, long long y)
{
    if (dp[(x - 1) * n + (y - 1)] == 0)
    {
        dp[(x - 1) * n + (y - 1)] = 1;
        cost++;
 
        if (x == n)
        {
            return;
        }
        else
        {
            stabilizePyramid(x + 1, y);
            stabilizePyramid(x + 1, y + 1);
        }
    }
}
 
int main()
{
    cin >> n >> m;
    dp = new int[n * n];
    memset(dp, 0, n * n * sizeof(int));
    cost = 0;
 
    for (long long i = 0; i < m; i++)
    {
        long long x, y;
        cin >> x >> y;
        stabilizePyramid(x, y);
    }
 
    delete dp;
 
    cout << cost;
 
    return 0;
}
{{< /highlight >}}
