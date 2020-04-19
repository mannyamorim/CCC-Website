---
title: "2020-S5 Josh\'s Double Bacon Deluxe"
---

# 2020-S5 Josh\'s Double Bacon Deluxe

{{< hint info >}}
**Problem Description**  
[View Problem](https://cemc.uwaterloo.ca/contests/computing/2020/stage%201/seniorEF.pdf)
{{< /hint >}}

## Key Insight #1

When we first look at this problem we think of a decision tree, however this approach will be far too slow. The first insight that we must see for this problem is how to use dynamic programming. The problem revolves around people who are disrupted (their burger is taken and they have to choose a random one). We can see that the last person to choose each of the burger types can potentially be disrupted:

1 2 3 **4** 1 1 **2** 3

As you can see the numbers in bold are the ones that may be disrupted. We know this since these are the last occurence of each burger type. Notice that we have left out 1 and 3. The burger that the coach chose (1) is a special case since if the coach chooses 1 then no one will be disrupted. The burger that Josh chose (3) has also been left out since we are not interested in what burger Josh will choose if he is disrupted, just the probability that he will be disrupted.

## Key Insight #2

Now that we know which people can be disrupted in order to use Dynamic Programming we need to show one more thing. An interesting property that we will show is that no matter how a person ends up being disrupted the subset of burgers still available to take will always be the same. We will use the example above:

1 2 3 **4** 1 1 **2** 3

We will consider what happens when 2 is disrupted. There are two possibilities:
- The coach can choose 2. This means that the subset of burgers taken is (2, 2, 3, 4, 1, 1) and the subset of burgers available is (1, 3).
- The coach can choose 4 and when 4 is disrupted they can choose 2. This means that the subset of burgers taken is (4, 2, 3, 2, 1, 1) and the subset of burgers available is (1, 3).

As you can see no matter how we end up at 2 being disrupted the subset of burgers available is always the same. This means that once we find the probability of Josh getting his burger after 2 being disrupted we can use this probability anywhere else in the problem since we can ignore what happened before.

## Key Insight #3

We will now combine what we have discovered above and create our recursive algorithm. First we will look at what happens with the coach. There are three possibilities:
- The coach chooses his own burger. In this case Josh is gaurenteed to get his.
- The coach chooses Josh's burger. In this case Josh is gaurenteed **not** to get his.
- The coach chooses someone else's burger and creates a disruption.

We will make a note that there is a special case if the coach and Josh choose the same burger. In this case Josh is gaurenteed to get his.

Now that we know how to find the final answer we just need to show how to solve the subproblems (finding the probability that Josh will get his burger when a specific disruption occurs). If a certain person is disrupted we again have three possibilities:
- They choose the coach's burger. In this case no further disruptions are caused and Josh gets his burger.
- They choose Josh's burger. In this case no further disruptions are caused and Josh does **not** get his burger.
- They choose someone else's burger and create another disruption.

By using the dynamic programming approach we can memoize the solutions to each of the subproblems. In this question it is actually easy for us to backtrack and compute answers to each of the subproblems starting from the last person in line and going toward the coach. Using this method each subproblem that we solve requires the answers to every subproblem before it.

{{< hint danger >}}
**Brute Force Implementation**  
A brute force solution that receives 9/15 points and uses the approach above.

[View Brute Force Solution]({{< relref "brute-force" >}})
{{< /hint >}}

## Key Insight #4

You may have thought that we have optimized the problem enough but it is still too slow to solve the last subtask. A carefull analysis of the previous solutions will show that since each subproblem requires the answers to all of the previous subproblems this will still require a lot of work to compute. The number of subproblems is based on how many disruptions can occur which is based on how many distinct burger orders there are which is M. The work done to compute the subtasks will look like (1 + 2 + 3 + ... + M - 2) steps. This sums up to ((M-2)^2)/2 which is simply O(M^2). This is still too slow for the final subtask where M can be up to 500000.

We need to linearize this part of the solution and we will see that we can use some clever math to do that. Going in reverse as we did above (starting from Josh moving to the coach) the solution to the ith subtask will look like:

{{< katex display >}}
s_i = \frac{c_{i-1}}{n_{old}} \cdot s_{i-1} + ... + \frac{c_1}{n_{old}} \cdot s_1 + \frac{c_{coach}}{n_{old}} \cdot 1
{{< /katex >}}

Where {{< katex >}} c_j {{< /katex >}} is the count or number of burgers of type j available and {{< katex >}} n_{old} {{< /katex >}} is the total number of burgers available to choose from for this subproblem. If we move to the next subproblem we will see:

{{< katex display >}}
s_{i+1} = \frac{c_i}{n_{new}} \cdot s_i + \frac{c_{i-1} + a_{i-1}}{n_{new}} \cdot s_{i-1} + ... + \frac{c_1 + a_1}{n_{new}} \cdot s_1 + \frac{c_{coach} + a_{coach}}{n_{new}} \cdot 1
{{< /katex >}}

We have used {{< katex >}} a_j {{< /katex >}} to denote the additional amount of j type burger that become available moving from subproblem i to j.

{{< katex display >}}
s_{i+1} = \frac{c_i}{n_{new}} \cdot s_i + \frac{n_{old}}{n_{new}} ( \frac{c_{i-1}}{n_{old}} \cdot s_{i-1} + ... + \frac{c_1}{n_{old}} \cdot s_1 + \frac{c_{coach}}{n_{old}} \cdot 1 ) + (\frac{a_{i-1}}{n_{new}} \cdot s_{i-1} + ... + \frac{a_1}{n_{new}} \cdot s_1 + \frac{a_{coach}}{n_{new}} \cdot 1)
{{< /katex >}}

{{< katex display >}}
s_{i+1} = \frac{c_i}{n_{new}} \cdot s_i + \frac{n_{old}}{n_{new}} \cdot s_i + (\frac{a_{i-1}}{n_{new}} \cdot s_{i-1} + ... + \frac{a_1}{n_{new}} \cdot s_1 + \frac{a_{coach}}{n_{new}} \cdot 1)
{{< /katex >}}

Using this formula we will still need to able to access the previous solutions but the number of operations will be dependent on how many burger are added (the additionals) in between this and the last potential disruption. So using this formula we have linearized the problem. We can now compute all of the solutions in O(N) time total.

{{< hint warning >}}
**Please attempt to solve the problem on your own before viewing the solution!**  
[View Solution]({{< relref "solution" >}})
{{< /hint >}}
