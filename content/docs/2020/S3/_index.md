---
title: "2020-S3 Searching for Strings"
---

# 2020-S3 Searching for Strings

{{< hint info >}}
**Problem Description**  
[View Problem](https://cemc.uwaterloo.ca/contests/computing/2020/stage%201/seniorEF.pdf)
{{< /hint >}}

## Key Insight #1

The first key insight is to understand how we can determing if substring of haystack is a permutation of needle. We will use a histogramming method to solve this part of the problem. Since the strings are only allowed to contain lowercase letters we can histogram using an array of length 26, one the stores the number of occurences in the string for each lowercase letter. We will let the length of needle be N and the length of haystack be H. Since we are looking for substrings of length N in haystack which are permutation of needle we will note that there are H - N + 1 possible positions for a substring to be in. We will need to compute the histogram of each of these substrings and compare it with the histogram of needle in order to find matches. Each match is a permutation of needle and will need to be considered.

## Key Insight #2

The method above for finding permutation is correct but slow. Computing the histogram of a substring of size N will require N steps (one for each character) making it an O(N) problem. If we want to compute the histograms of H - N + 1 substrings of length N we will have N * (H - N + 1) which is not optimal. We will see that we can do better.

The second key insight is that if we process the possible substrings in seqential order we can compute the histograms in constant time. Since each substring differs by only one charcter to the substring in the next position, given the histogram of the previous substring we can compute the next histogram in constant time. Moving to the next substring entail removing one character from the front of the string and adding one to the back. From this we can see that we can modify our histogram in two operations to be the histogram of the next substring. This effectively makes the histogramming part of the problem to be constant time.

We of course need to find the histogram of the first substring which will take O(N) steps. Then we will need to find (H - N + 1) - 1 additional histograms. Where each histogram takes a constant number of steps. So we take O(H - N) steps to find the rest of the histograms. So in total we take O(N) + O(H - N) steps which is just O(H) steps. This is a big improvement and will allow us to complete the problem within the time limit.

## Key Insight #3

Since we want to find the number of **unique** permutations of needle in haystack. We will use the method above to find permutations and then we will simply store them in a hashset since the hashset will only store unique elements. Once all of the permutations have been added to the hashset we will simply output the number of elements in the hashset as our final answer.

{{< hint warning >}}
**Please attempt to solve the problem on your own before viewing the solution!**  
[View Solution]({{< relref "solution" >}})
{{< /hint >}}
