---
title: "2016-S2 Tandem Bicycle"
---

# 2016-S2 Tandem Bicycle

{{< hint info >}}
**Problem Description**  
[View Problem](https://cemc.uwaterloo.ca/contests/computing/past_ccc_contests/2016/stage%201/seniorEn.pdf)
{{< /hint >}}

## Key Insight #1 (Minimum)

To minimize the total speed sum we have to match the bicycles as closely together in speed as possible. The best way to do that is to sort the cyclists from slowest to fastest and combine them together. 

Given this example:

| Country   | 1      | 2     | 3      | 4     | 5     | Total |
|-----------|--------|-------|--------|-------|-------|-------|
| Dmojistan | 9      | **3** | **15** | 2     | **5** | 34    |
| Pegland   | **19** | 2     | 14     | **7** | 1     | 43    |
| MAX()     | 19     | 3     | 15     | 7     | 5     | 49    |

Sorting and combining them together produces:

| Country   | 1     | 2     | 3     | 4      | 5      | Total |
|-----------|-------|-------|-------|--------|--------|-------|
| Dmojistan | **2** | **3** | 5     | 9      | 15     | 34    |
| Pegland   | 1     | 2     | **7** | **14** | **19** | 43    |
| MAX()     | 2     | 3     | 7     | 14     |  19    | 49    |

Note that there are several ways to produce the minimum score of 45 (swapping cyclists 1 and 2 of the Dmojistan team results in the same score) but this approach always produces a combination with the minimum score.

## Key Insight #2 (Maxiumum)

To obtain the maximum speed we want to combine the fastest cyclists from one team with the slowest from the other team. The best way to do that is sort one team from slowest to fastest and sort the other team from fastest to slowest. This will always produce a maximum result.

Given this example:

| Country   | 1      | 2     | 3      | 4     | 5     | Total |
|-----------|--------|-------|--------|-------|-------|-------|
| Dmojistan | 9      | **3** | **15** | 2     | **5** | 34    |
| Pegland   | **19** | 2     | 14     | **7** | 1     | 43    |
| MAX()     | 19     | 3     | 15     | 7     | 5     | 49    |

Sorting and combining them together produces:

| Country   | 1      | 2      | 3     | 4     | 5      | Total |
|-----------|--------|--------|-------|-------|--------|-------|
| Dmojistan | 2      | 3      | 5     | **9** | **15** | 34    |
| Pegland   | **19** | **14** | **7** | 2     | 1      | 43    |
| MAX()     | 2      | 3      | 7     | 14    |  19    | 49    |

Once again, there are multiple ways of producing this maximum score but this approach always leads to a maximum score.

{{< hint warning >}}
**Please attempt to solve the problem on your own before viewing the solution!**  
[View Solution]({{< relref "solution" >}})
{{< /hint >}}
