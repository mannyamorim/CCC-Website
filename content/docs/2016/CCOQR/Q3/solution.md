---
title: "2016-CCOQR-Q3 Data Structure - Solution"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2016-CCOQR-Q3 Data Structure - Solution

## Reading Inputs

The input is simply read into a point vector. Note that the row is adjusted to be from the bottom and the columns is set to start from 0.

{{< highlight cpp >}}
typedef struct {
    long row;
    long col;
} point;
 
long totalRows;
long totalPoints;
 
std::vector<point> points;
 
void read_input()
{
    std::cin >> totalRows >> totalPoints;
    points.reserve(totalPoints);
    for (int i=0;i<totalPoints;i++)
    {
        long row,col;
        std::cin >> row >> col;
 
        // Adjust rows and columns
        point p;
        p.row = totalRows - row + 1;
        p.col = col-1;
 
        // Save in vector
        points.push_back(p);
    }
}
{{< /highlight >}}

## Sort The Points

The points need to be sorted for this method to work.

{{< highlight cpp >}}
bool compare_points(point a,point b)
{
    return (a.col<b.col);
}
void sort_points()
{
    // Sort Points
    std::sort(points.begin(),points.end(),compare_points);
}
{{< /highlight >}}

## Point Access Methods

The algorithm needs to know if one or more points exist at this column and if they what the maximum row (from bottom to top).

{{< highlight cpp >}}
int curPoint = 0;
 
bool is_there_point_at_this_column(long column)
{
    if (curPoint >= totalPoints)
        return false;
 
    if (points[curPoint].col == column)
        return true;
    else
        return false;
}
long get_maximum_rows_at_column(long column)
{
    long maxRows = 0;
 
    while (is_there_point_at_this_column(column) == true)
    {
        if (points[curPoint].row > maxRows)
            maxRows = points[curPoint].row;
 
        curPoint++;
    }
 
    return maxRows;
}
{{< /highlight >}}

## Solver

This method goes through all the columns and computes the total sum of the rows above each column while maintaining the previous row count.

{{< highlight cpp >}}
void solve()
{
    long long totalSum = 0;
    long prevRow = 0;
  
    // Process the columns
    for (long col=0;col<totalRows;col++)
    {
  
        // Is there a point on this column
        if (is_there_point_at_this_column(col))
        {
            // Get the maximum rows on this column (there might be multiple points)
            long rows = get_maximum_rows_at_column(col);
  
            // Only if we are higher than previous rows do we store this value
            if (rows > prevRow)
                prevRow = rows;
        }
  
        // Add the row count to the sum
        totalSum += prevRow;
  
        // Reduct the row count by one until we are at zero
        if (prevRow > 0)
            prevRow--;
    }
 
    // Write Result
    std::cout << totalSum;
}
{{< /highlight >}}
