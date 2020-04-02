---
title: "2017-S3 Nailed It!"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2017-S3 Nailed It!

{{< hint info >}}
**Problem Description**  
View Problem
{{< /hint >}}

## Key Insight #1

This problem may initially seem like a recursive dynamic programming solution. However, the easiest way to solve this problem is to iterate through all the possible board heights and count the number of boards that can be formed at that height. Each piece of wood can be between 1 and 2000 units long. This means that the boards can be between 2 and 4000 units long. This solution will run in time because there are so few board heights.

## Key Insight #2

To find the number of boards possible at a specific height quickly we will need to use a counting sort. A counting sort is a special sort algorithm that runs in linear time. The algorithm is only suitable when sorting relatively small integers which is what our problem uses. To perform a counting sort we initialize an array as large as the largest value being sorted (in this case 2000). The array is initialized to 0 and as each value is read in the corresponding cell is incremented by one. At the end of the process the array will hold a value showing how many pieces of wood we have at each possible length.

## Key Insight #3

If we have the inputs counting sorted it is easy for us to determine the number of boards that can be formed at a specific height. If we call the height H, then the possible combinations start at 1 + (H - 1), 2 + (H - 2), and so on. This is easy to implement with two index variables. For each combination we read the value of the counting sort array at each index and the minimum is the number of boards that can be formed using that combination. The number of boards formed should then be subtracted from the array.

## Corner Cases

There are a few corner cases to watch out for when programming this problem. These include:

1. If both iterators are the same. For example if the height you are looking for is 10 and the iterators are 5 and 5. This case requires a special if statement.
2. When the height is higher than 2000. It is invalid for the individual pieces of wood to have a length of more than 2000. This means that you cannot use 1 to create the first iterator. You must use max(1, height - 2000) and (height - first iterator) for the second.

## Optimizations

1. After creating this initial counting sort array. We need to make a copy of it each time so that we do not change the original. This can be done quickly with C's memcpy.

{{< hint warning >}}
**Please attempt to solve the problem on your own before viewing the solution!**  
View Solution
{{< /hint >}}
