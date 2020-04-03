---
title: "2016-S4 Combining Rice Balls - Solution"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2016-S4 Combining Rice Balls - Solution

## Reading Inputs

The inputs are extremely simple to read:

{{< highlight cpp >}}
int totalBalls;
int riceBalls[400];
 
void read_input()
{
    std::cin >> totalBalls;
    for (int i=0;i<totalBalls;i++)
        std::cin >> riceBalls[i];
}
{{< /highlight >}}

## Initializing the Dynamic Programming Container

We are using a 2D array for the DP container that simply needs to be initialized to -1 at all locations. 

{{< highlight cpp >}}
int counts[400][400];
 
void init_dp()
{
    for (int start=0;start<totalBalls;start++)
        for (int end=0;end<totalBalls;end++)
            counts[start][end] = -1;
}
{{< /highlight >}}

When a match is found we will call a method to set the total count of the range into the array:

{{< highlight cpp >}}
void add_solution(int startIndex, int endIndex, int ballCount)
{
    counts[startIndex][endIndex] = ballCount;
}
{{< /highlight >}}

We also have a method to see if a solution has been found for a specific range. If the start and end range index locations are the same we always return TRUE.

{{< highlight cpp >}}
bool can_range_be_combined(int startIndex, int endIndex)
{
    if (startIndex == endIndex)
        return true;
    else
        return counts[startIndex][endIndex] != -1;
}
{{< /highlight >}}

A final method simply returns the total rice balls contained in a range (this functions assumes a combination is possible):

{{< highlight cpp >}}
int get_range_count(int startIndex, int endIndex)
{
    if (startIndex == endIndex)
        return riceBalls[startIndex];
    else
        return counts[startIndex][endIndex];
}
{{< /highlight >}}

## Simple Search Method

We have to have a search method that search for matches with only two blocks (first and last) and no middle block. This is the simplest search method and it returns TRUE or FALSE if it finds a match:

{{< highlight cpp >}}
// Looks for two blocks that can be combined and contain equal number of rice_balls
bool search_range_for_two_blocks(int firstStartIndex, int lastEndIndex)
{
    for (int lastStartIndex = firstStartIndex+1; lastStartIndex <= lastEndIndex; lastStartIndex++)
    {
        // Define the first range
        int firstEndIndex = lastStartIndex-1;
 
        // Skip starting ranges that cannot be combined
        if (can_range_be_combined(firstStartIndex,firstEndIndex) == false)
            continue;
 
        // Skip ending ranges that cannot be combined
        if (can_range_be_combined(lastStartIndex,lastEndIndex) == false)
            continue;
 
        // We need to know the first range count
        int firstCount = get_range_count(firstStartIndex,firstEndIndex);
 
        // We need to know the last range count
        int lastCount = get_range_count(lastStartIndex,lastEndIndex);
 
        // We can only combine if the first range count is the same as the last range count
        if (firstCount != lastCount)
            continue;
 
        // Compute the combined number of rice balls
        int rangeCount = firstCount + lastCount;
 
        // We have found a solution - lets add it to the our DP container
        add_solution(firstStartIndex,lastEndIndex,rangeCount);
 
        // Only one solution is important - we don't have to keep looking for another
        return true;
    }
  
    // We did not find a solution
    return false;
}
{{< /highlight >}}

The second solver is more complex and needs to find matches with three blocks (first, middle, last):

{{< highlight cpp >}}
// Looks for solutions with a middle block that can be combined
void search_range_for_three_blocks(int firstStartIndex, int lastEndIndex)
{
    for (int middleStartIndex = firstStartIndex+1; middleStartIndex < lastEndIndex; middleStartIndex++)
    {
        // Define the end of the first block
        int firstEndIndex = middleStartIndex-1;
 
        // Skip first range if it cannot be combined
        if (can_range_be_combined(firstStartIndex,firstEndIndex) == false)
            continue;
 
        // We need to know the starting range count
        int firstCount = get_range_count(firstStartIndex,firstEndIndex);
         
        for (int lastStartIndex = middleStartIndex+1; lastStartIndex <= lastEndIndex; lastStartIndex++)
        { 
            // Define the end of them middle block
            int middleEndIndex = lastStartIndex -1;
 
            // Skip middle range that cannot be combined
            if (can_range_be_combined(middleStartIndex,middleEndIndex) == false)
                continue;
 
            // Skip last range that cannot be combined
            if (can_range_be_combined(lastStartIndex,lastEndIndex) == false)
                continue;
 
            // We need to know the last range count
            int lastCount = get_range_count(lastStartIndex,lastEndIndex);
 
            // We can only combine if the first range count is the same as the last range count
            if (firstCount != lastCount)
                continue;
 
            // We need the middle range count so we can compute the full range count
            int middleCount = get_range_count(middleStartIndex,middleEndIndex);
            int rangeCount = firstCount + middleCount + lastCount;
 
            // We have found a solution - lets add it to the our DP container
            add_solution(firstStartIndex,lastEndIndex,rangeCount);
 
            // Only one solution is important - we don't have to keep looking for another
            return;
        } 
    }
}
{{< /highlight >}}

The final solver puts the entire solution together by search for solutions first with 2 rice balls, then 3, until it tries to combine all the balls together:
Search

{{< highlight cpp >}}
// Solve for ranges of 2 and all the way to the totalBalls
for (int range=2;range<=totalBalls;range++)
{
    int maxStart = totalBalls-range+1;
    for (int firstStartIndex=0;firstStartIndex<maxStart;firstStartIndex++)
    {
        int lastEndIndex = firstStartIndex + range - 1;
 
        // Search for two block solutions (first,last)
        bool found = search_range_for_two_blocks(firstStartIndex,lastEndIndex);
 
        // If we found a solution with the simple solver don't do any more work
        if (found) continue;
 
        // Search for solutions with three blocks (first, middle, last) when we have at least 3 balls
        if (range > 2)
            search_range_for_three_blocks(firstStartIndex,lastEndIndex);
    }
}
{{< /highlight >}}

## Final Step

The final step is to find the largest ball in our solution set. This is a simple 2D search:

{{< highlight cpp >}}
// Quick search through all solutions for the one with the largest number of riceBalls
int find_largest_combination()
{
    int max = 0;
 
    for (int start=0;start<totalBalls;start++)
        for (int end=start;end<totalBalls;end++)
            max = std::max( max, get_range_count(start,end) );
 
    return max;
}
{{< /highlight >}}
