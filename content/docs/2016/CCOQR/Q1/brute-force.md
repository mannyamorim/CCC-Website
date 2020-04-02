---
title: "2016-CCOQR-Q1 Stupendous Bowties - Brute Force"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2016-CCOQR-Q1 Stupendous Bowties - Brute Force Solution

## Implementation

{{< highlight cpp >}}
#include <algorithm>
#include <iostream>
#include <map>
 
using namespace std;
typedef long long int64;
 
int numberOfPoints;
multimap<int, int> pointsXSorted;
multimap<int, int> pointsYSorted;
 
void readInput()
{
    cin >> numberOfPoints;
    pointsXSorted = multimap<int, int>();
    pointsYSorted = multimap<int, int>();
    for (int i = 0; i < numberOfPoints; i++)
    {
        int x, y;
        cin >> x >> y;
        pointsXSorted.insert(pair<int, int>(x, y));
        pointsYSorted.insert(pair<int, int>(y, x));
    }
}
 
int64 getBowties(int x, int y)
{
    int64 xSameUp = 0, xSameDown = 0, ySameLeft = 0, ySameRight = 0;
    auto sameX = pointsXSorted.equal_range(x);
    auto sameY = pointsYSorted.equal_range(y);
    for (auto it = sameX.first; it != sameX.second; it++)
    {
        if (it->second > y)
        {
            xSameDown++;
        }
        else if (it->second < y)
        {
            xSameUp++;
        }
    }
    for (auto it = sameY.first; it != sameY.second; it++)
    {
        if (it->second > x)
        {
            ySameRight++;
        }
        else if (it->second < x)
        {
            ySameLeft++;
        }
    }
    if (xSameUp > 0 && xSameDown > 0 && ySameLeft > 0 && ySameRight > 0)
    {
        return xSameUp * xSameDown * ySameLeft * ySameRight * 2;
    }
    else
    {
        return 0;
    }
}
 
int64 solve()
{
    int64 bowties = 0;
    for (auto it = pointsXSorted.begin(); it != pointsXSorted.end(); it++)
    {
        bowties += getBowties(it->first, it->second);
    }
    return bowties;
}
 
int main()
{
    readInput();
    int64 bowties = solve();
    cout << bowties;
    return 0;
}
{{< /highlight >}}
