---
title: "2020-S4 Swapping Seats"
---

# 2020-S4 Swapping Seats

{{< hint info >}}
**Problem Description**  
[View Problem](https://cemc.uwaterloo.ca/contests/computing/past_ccc_contests/2020/ccc/seniorEF.pdf)
{{< /hint >}}

## Key Insight #1

The first insight in this problem is that we cannot look at what swaps we can make as this will take too long, rather we must look at where we want the groups to be sitting in the end. If we decide on the exact positions for each of the groups we can compute the minimal number of swaps required to rearrange everyone into those positions in constant time. We will need to know who is sitting in these ranges right now but we will show how to compute that quickly later.

We will demonstrate with the following example:

    AAABBCBBACAAC

As you can see there are 6 A's, 4 B's and 3 C's. We will decide for this example that we want them to be rearranged into groups with the A group starting first then the B and the C being last. We will start the A group off at the very left edge.

    |AAABBC|BBAC|AAC|

As you can see we have marked where the groups will be and the size of the groups is according to the number of A's, B's, or C's.

|                 | A Group | B Group | C Group |
|-----------------|---------|---------|---------|
| A's Present Now | 3       | 1       | 2       |
| B's Present Now | 2       | 2       | 0       |
| C's Present Now | 1       | 1       | 1       |

In the table above we have tallied who is currently in the ranges that we have decided on. We will now compute the number of swaps required to rearrange everyone. The first step is to make the swaps that fix two problems at once (ie. A's in the B range swapped with B's in the A range).

    min(A.B, B.A) = 1
    min(B.C, C.B) = 0
    min(A.C, C.A) = 1

This shows that we can make two swaps. Once from A to B and one from A to C. This will give us the following table:

|                 | A Group | B Group | C Group |
|-----------------|---------|---------|---------|
| A's Present Now | 5       | 0       | 1       |
| B's Present Now | 1       | 3       | 0       |
| C's Present Now | 0       | 1       | 2       |

We now need to do the less effecient swaps. We have one B in A and one C in B and one A in C. For each three people to be moved in this type of rotation we need to do 2 swaps. So we need to do 4 swaps in total to get to this arrangement.

## Key Insight #2

Now that we know how to calculate the swaps required to rearrange everyone into specific groups, we need to decide on what the groups will be. Once we decide on an ordering we can start at N positions around the circle so we need to try all of them. We can linearize the problem by using one pass to create the table above with the totals. Then to move to the next position we just need to adjust each of the 3 ranges by adding and removing one person. This will take 6 operations and so will be constant time. Since we can incrementally move to the next position and calculate the number of swaps in constant time, we can try N positions in O(N) time.

## Key Insight #3

We mentioned ordering above. This refers to the relative ordering of the groups of people in the final arrangment around the table. For three elements there are `3!` permutations. So for `ABC` there is `ABC, ACB, BAC, BCA, CAB, CBA`. Since the table is circular and we will try every starting point a lot of these permutation are redundant. We can see that `ABC, BCA, CAB` are all equivalent since we are using a circular table and `CBA, BAC, ACB` are also equivalent. We will only need to try one ordering from each of these groups of 3. So in fact since we are using a circular table we will need to try `3! / 3` different orderings which is just 2.

## Algorithm

So roughly our algorithm is:
- Make a pass through the array to compute the totals so that we know the size of the groups
- Keep a running minimum of the number of swaps needed
- Try using the `ABC` ordering:
    - Make a pass through the array to compute the totals for each of the initial ranges
    - Loop through the N starting positions
        - Compute the number of swaps required and update minimum if required
        - Update the totals incrementally to prepare for the next position
- Repeat the above steps using the `CBA` ordering

{{< hint warning >}}
**Please attempt to solve the problem on your own before viewing the solution!**  
[View Solution]({{< relref "solution" >}})
{{< /hint >}}
