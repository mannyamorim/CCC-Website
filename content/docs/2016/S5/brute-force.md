---
title: "2016-S5 Circle of Life - Brute Force"
date: 2020-04-01T13:36:55-04:00
draft: false
---

# 2016-S5 Circle of Life - Brute Force Solution

## Algorithm

The approach in this implementation is to store the 0s and 1s as bits and create the fastest possible bit shifting algorithm to compute the Circle of Life problem directly.

## Testing Results

| Task# | Test# | Cells   | Iterations            | Execution Time         |
|-------|-------|--------:|----------------------:|------------------------|
| 1     | 1     | 15      | 15                    | 0.49 seconds           |
| 2     | 2     | 200     | 200                   | 0.39 seconds           |
| 2     | 3     | 2,000   | 500,000               | 0.64 seconds           |
| 2     | 4     | 4,000   | 10,000,000            | 1.189 seconds          |
| 2     | 5     | 4,000   | 10,000,000            | 1.185 seconds          |
| 2     | 6     | 10      | 20,000,000            | 0.079 seconds          |
| 2     | 7     | 15      | 100,000,000           | 0.254 seconds          |
| 3     | 8     | 15      | 100,000,000           | 0.285 seconds          |
| 3     | 9     | 15      | 1,000,000,000         | 2.258 seconds          |
| 3     | 10    | 15      | 1,000,000,000,000     | 36 minutes (estimated) |
| 3     | 11    | 15      | 1,000,000,000,000,000 | 25 days (estimated)    |
| 4     | 12    | 10,000  | 10,000,000,000        | 20 minutes (estimated) |
| 4     | 13    | 10,000  | 10,000,000            | 2.993 seconds          |
| 4     | 14    | 100,000 | 562,949,953,421,311   | 49 years (estimated)   |
| 4     | 15    | 100,000 | 1,000,000,000,000,000 | 86 years (estimated)   |

## Implementation

{{< highlight cpp >}}
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
 
#define restrict __restrict__
 
typedef unsigned long long u64;
 
u64 bitMask;
long blocks;
long extrabits;
long bitCount;
long long iterationCount;
u64* bitBuffer;
u64* leftBuffer;
u64* rightBuffer;
 
void init_bits(long bits)
{
   blocks = (bits + 63) / 64; // Round to the nearest block
   extrabits = bits % 64;    // How many bits are left over
 
   u64 start = 0x8000000000000000;
 
   u64 v = start;
   for (int i = 0; i < extrabits - 1; i++)
      v = (v >> 1) | start;
 
   bitMask = v;
 
   bitBuffer = new u64[blocks];
   leftBuffer = new u64[blocks];
   rightBuffer = new u64[blocks];
}
 
void clean_bits(u64 *buffer)
{
   // Cleanup the final word
   if (extrabits != 0)
      buffer[blocks - 1] = buffer[blocks - 1] & bitMask;
}
 
 
void circle_shift_right(u64 const * restrict input, u64 * restrict output)
{
   // Get the first previous bit from the end of the array
   u64 prevbit;
   if (extrabits == 0)
      prevbit = input[blocks - 1] << 63;
   else
      prevbit = (input[blocks - 1] << (extrabits - 1)) & 0x8000000000000000;
 
   // Shift the entire array
   for (int b = 0; b < blocks; b++)
   {
      output[b] = (input[b] >> 1) | prevbit;  // Shift automatically leaves a 0
      prevbit = input[b] << 63; // Shift automatically fills with 0s
   }
 
   clean_bits(output);
}
 
 
 
void circle_shift_left(u64 const * restrict input, u64 * restrict output)
{
   // Get the first previous bit as the first bit in the array
   u64 prevbit = (input[0] & 0x8000000000000000) >> (extrabits - 1);
 
 
   // Shift the entire array
   for (int b = blocks - 1; b >= 0; b--)
   {
      output[b] = (input[b] << 1) | prevbit;  // Shift automatically leaves a 0
      prevbit = input[b] >> 63; // Shift automatically fills with 0s
   }
}
 
 
void xor_bits(u64 const * restrict leftBits, u64 const * restrict rightBits, u64 * restrict output)
{
   for (int i = 0; i < blocks; i++)
      output[i] = leftBits[i] ^ rightBits[i];
}
 
 
void text_to_bits(const char * restrict text, u64 * restrict input, int bits)
{
   // Dump the bits in the block
 
   for (int b = 0; b < blocks; b++)
   {
      u64 curBit = 0x8000000000000000;
      u64 curValue = 0;
      int maxBits = std::min(bits, 64);
 
      for (int bit = 0; bit < maxBits; bit++)
      {
         if (*text == '1')
            curValue |= curBit;
 
         curBit = curBit >> 1;
         text++;
      }
 
      bits -= maxBits;
      *input = curValue;
      input++;
   }
}
 
std::string bits_to_text(u64 const *restrict input, int bits)
{
   char* text = new char[bits + 1];
   char *c = text;
 
   // Dump the bits in the block
   for (int b = 0; b < blocks; b++)
   {
      int curbits = std::min(bits, 64);
      bits -= curbits;
 
      for (int i = 0; i < curbits; i++)
      {
         if (((input[b] & (0x8000000000000000 >> i)) != 0))
            *c = '1';
         else
            *c = '0';
         c++;
      }
   }
 
   *c = '\0';
   return std::string(text);
}
 
//----------------------------------------------------------------
// INPUT
//----------------------------------------------------------------
 
void read_input()
{
   std::string input;
 
   std::cin >> bitCount >> iterationCount;
   std::getline(std::cin, input);
 
   init_bits(bitCount);
 
   std::getline(std::cin, input);
 
   text_to_bits(input.c_str(), bitBuffer, bitCount);
}
 
void write_output()
{
   std::cout << bits_to_text(bitBuffer, bitCount);
}
  
 
//----------------------------------------------------------------
// SOLVER
//----------------------------------------------------------------
 
void compute_iteration()
{
   // shift bits both left and right
   circle_shift_left(bitBuffer, leftBuffer);
   circle_shift_right(bitBuffer, rightBuffer);
 
   // Combine into result using XOR operation
   xor_bits(leftBuffer, rightBuffer, bitBuffer);
}
 
void solve_long_iterations()
{
   for (long long iteration = 0; iteration < iterationCount; iteration++)
      compute_iteration();
}
 
void solve_short_iterations()
{
   // Get the Values
   u64 value = bitBuffer[0];
   u64 left, right;
  
   for (long long iteration = 0; iteration < iterationCount; iteration++)
   {
      left = value << 1;
      right = (value >> 1) & bitMask;
 
      left |= (value & 0x8000000000000000) >> (extrabits - 1);
      right |= (value << (extrabits - 1)) & 0x8000000000000000;
 
      value = left ^ right;
   }
  
   bitBuffer[0] = value;
}
 
int main()
{
   read_input();
 
   if (bitCount > 64)
      solve_long_iterations();
   else
      solve_short_iterations();
 
   write_output();
  
   return 0;
}
{{< /highlight >}}
