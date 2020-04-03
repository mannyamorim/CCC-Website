---
title: "2016-S5 Circle of Life - Solution"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2016-S5 Circle of Life - Solution

## Declare Variables

{{< highlight cpp >}}
int *oldGeneration;
int *newGeneration;
int n;
long long t;
{{< /highlight >}}

## Read Inputs and Write Output

{{< highlight cpp >}}
void readInput()
{
    std::cin >> n >> t;
    oldGeneration = new int[n];
    newGeneration = new int[n];
    std::string input;
    std::getline(std::cin, input);
    std::getline(std::cin, input);
    for (int i = 0; i < n; i++)
    {
        if (input.c_str()[i] == '0')
            oldGeneration[i] = 0;
        else
            oldGeneration[i] = 1;
    }
}
 
void writeOutput()
{
    for (int i = 0; i < n; i++)
    {
        std::cout << oldGeneration[i];
    }
    delete oldGeneration;
    delete newGeneration;
}
{{< /highlight >}}

## Get Cyclic Pointer

The list of cells wraps around. We need a function that given a cyclic index returns the real index of the cell.

{{< highlight cpp >}}
int getCyclicPointer(long long cyclicIndex)
{
    long long nonCyclicIndex = cyclicIndex % n;
    if (nonCyclicIndex < 0)
        return n + nonCyclicIndex;
    else
        return nonCyclicIndex;
}
{{< /highlight >}}

## Simulate Generations

{{< highlight cpp >}}
void simulateGenerations(long long numberOfGenerations)  //Number of generation must be a power of two
{
    for (int i = 0; i < n; i++)
    {
        newGeneration[i] = oldGeneration[getCyclicPointer(i - numberOfGenerations)] ^ oldGeneration[getCyclicPointer(i + numberOfGenerations)];
    }
}
{{< /highlight >}}

## Swap Generation Pointers

{{< highlight cpp >}}
void swapGenerationPointers()
{
    int* intermediate = oldGeneration;
    oldGeneration = newGeneration;
    newGeneration = intermediate;
}
{{< /highlight >}}

## Get Largest Power of Two

{{< highlight cpp >}}
long long getLargestPowerOfTwo(long long max)
{
    unsigned long long powerOfTwo = 1;
    for (int i = 0; i < 64; i++)
    {
        powerOfTwo <<= 1;
        if (powerOfTwo > max)
        {
            powerOfTwo >>= 1;
            return (long long)powerOfTwo;
        }
    }
    return 0;
}
{{< /highlight >}}

## Main Method

{{< highlight cpp >}}
int main()
{
    readInput();
    long long generationsLeft = t;
    while (generationsLeft > 0)
    {
        long long largestPowerOfTwo = getLargestPowerOfTwo(generationsLeft);
        generationsLeft -= largestPowerOfTwo;
        simulateGenerations(largestPowerOfTwo);
        swapGenerationPointers();
    }
    writeOutput();
    return 0;
}
{{< /highlight >}}
