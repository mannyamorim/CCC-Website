---
title: "2016-S2 Tandem Bicycle - Solution"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2016-S2 Tandem Bicycle - Solution

## Read Input

{{< highlight cpp >}}
long question;
long totalPeople;
std::vector<long> A,B;
 
void read_input()
{
    std::cin >> question >> totalPeople;
    A.reserve(totalPeople);
    B.reserve(totalPeople);
 
    for (int i=0;i<totalPeople;i++)
    {
        int v;
        std::cin >> v;
        A.push_back(v);
    }
 
    for (int i=0;i<totalPeople;i++)
    {
        long v;
        std::cin >> v;
        B.push_back(v);
    }
}
{{< /highlight >}}

## Sort Methods

The following functions simplify the C++ STD::SORT methods:

{{< highlight cpp >}}
bool compare_increasing(long a,long b)
{
    return (a<b);
}
  
 
void sort_increasing(std::vector<long> & vector)
{
    std::sort(vector.begin(),vector.end(),compare_increasing);
}
  
 
bool compare_decreasing(long a, long b)
{
    return (a>b);
}
  
 
void sort_decreasing(std::vector<long> & vector)
{
    std::sort(vector.begin(),vector.end(),compare_decreasing);
}
{{< /highlight >}}

## Solver

The key of the algorithm is choosing which way to sort and then computing the sum.

{{< highlight cpp >}}
void arrange_bikers()
{
    sort_increasing(A);
  
 
    // Maximum?
    if (question == 2)
        sort_decreasing(B);
    else
        sort_increasing(B);
}
 
 
long computeMaxSum()
{
    long sum = 0;
    for (int i=0;i<totalPeople;i++) 
        sum += std::max(A[i],B[i]);
    return sum;
}
{{< /highlight >}}
