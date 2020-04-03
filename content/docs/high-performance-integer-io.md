---
title: "High Performance Integer C++ IO"
---

# High Performance Integer C++ IO

This page covers some of the lessons learned when writing solutions in C++ for programming contest problems. Many of these problems require a substantial number of integer values to be read in and this can contribute significantly to the over execution time. This page covers some of the methods that can be used to reduce this time penalty.

## Testing Method

To evaluate different methods of reading integer data, a large text input file of 100 million integer numbers was used. These numbers are read using the function below to compute a final sum (simply to keep the code from being optimized away):

{{< highlight cpp >}}
void compute_sum()
{
    int total_numbers = get_next_int();
    long long sum = 0;
    for (int i = 0; i < total_numbers; i++)
        sum += get_next_int();
    printf("%lld\n", sum);
}
{{< /highlight >}}

## 1) C++ STD::CIN

The most standard approach to reading numbers is to use the C++ standard library. This is simple and easy to use:

{{< highlight cpp >}}
int get_next_int()
{
    int number;
    std::cin >> number;
    return number;
}
{{< /highlight >}}

## 2) C++ STD::CIN (Untied and Unsync streams)

Using the approach above, you can increase performance if you:

1.  Disable the synchronization between C and C++ standard streams
2.  Untie the cin and cout streams

For contests, this is safe to do as long as you don't mix C and C++ stream IO:

{{< highlight cpp >}}
void init_io()
{
    std::cin.sync_with_stdio(false);
    std::cin.tie(NULL);
}

int get_next_int()
{
    int number;
    std::cin >> number;
    return number;
}
{{< /highlight >}}

## 3) C Scanf

This is the old C standard way of reading in data:

{{< highlight cpp >}}
int get_next_int()
{
    int number;
    scanf("%d", &number);
    return number;
}
{{< /highlight >}}

## 4) Dedicated Integer Reader

This function is only useful when all the input is integer numbers (it cannot be used with any other input methods):

{{< highlight cpp >}}
const int max_buffer_size = 1048576; // 1 Megabyte
char buffer[max_buffer_size];
char* maxChar = buffer;
char* curChar = buffer;
 
inline char get_next_char()
{
    if (curChar == maxChar)
    {
        // Fill Buffer
        int read = fread(buffer, 1, max_buffer_size, stdin);
        maxChar = buffer + read;
        curChar = buffer;
    }
    return *(curChar++);
}

int get_next_int()
{  
    // Skip to next int
    char c;
    do
        c = get_next_char();
    while ((c < '0' || c > '9') && (c != '-'));
 
    // Deal with Negative Numbers
    int sign = 1;
    if (c == '-')
    {
        sign = -1;
        c = get_next_char();
    }
 
    // Compute the actual number
    int number = 0;
    do
    {
        number = (number * 10) + (c - '0');
        c = get_next_char();
    } while (c >= '0' && c <= '9');
 
    // Final Number
    return number * sign;
}
{{< /highlight >}}

## 5) Raw IO

The most basic comparison is the raw reading from a file. This reads the entire input file into memory one block at a time without the conversion to numbers.

{{< highlight cpp >}}
#include <stdio.h>
const int max_buffer_size = 1048576; // 1 Megabyte
char buffer[max_buffer_size];

void raw_io()
{
    int total_to_read = 582398894;
    while (total_to_read > 0)
    {
        // Fill Buffer
        int read = fread(buffer, 1, max_buffer_size, stdin);
        total_to_read -= read;
    }
}
{{< /highlight >}}

## Results

For all timing results, the test was run four times with the slowest result discarded and the remaining the results averaged.

The testing system was a Windows 10 64-bit machine with an intel i5-2500K (overclocked to 4.3Ghz) and 16GB of RAM.

| Method                                   | Execution Time   | Numbers Read per Second |
|------------------------------------------|------------------|-------------------------|
| C++ STD::CIN                             | 61.14 seconds    | 1,635,455               |
| C++ STD::CIN (Untied and Unsync streams) | 51.45 seconds    | 1,943,322               |
| C Scanf                                  | 24.76 seconds    | 4,037,397               |
| **Dedicated Integer Reader**             | **1.89 seconds** | **52,678,897**          |
| Raw IO                                   | 0.64 seconds     | -                       |

## Conclusions

On a contest with a 3 second budget for solving the problem you have to be very careful what time you spend doing IO. My recommendation is that the IO take no more than 5% of your overall budget which means that it has to be completed in less than **150 milliseconds**. Here are some recent contests and how they would fare on their largest test case:

| Contest               | Max Integers | 1) CIN | 2) CIN | 3) Scanf | 4) Reader |
|-----------------------|--------------|--------|--------|----------|-----------|
| Through A Maze Darkly | 600,003      | 367 ms | 316 ms | 145 ms   | 11 ms     |
| Stupendous Bowties    | 200,202      | 84 ms  | 73 ms  | 33 ms    | 5 ms      |

Overall conclusions:

1.  Always use **Scanf** since it is simple to implement and will keep the IO costs down
2.  In case of large datasets or if your solution is close to the edge - use the reader.