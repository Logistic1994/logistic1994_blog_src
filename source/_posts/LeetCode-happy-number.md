title: LeetCode_happy_number
date: 2015-09-13 13:30:20
category: C
tags: [OJ]
---

## Description
> A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits and repeat the process until the number equals 1(where it will stay), or it loops endlessly in a cycle which does not include 1. Those numbers for which this process ends in 1 are happy numbers.
> **Example**: 19 is a happy number
> $$
> 1^2 + 9^2 = 82 \\\
> 8^2 + 2^2 = 68 \\\
> 6^2 + 8^2 = 100 \\\
> 1^2 + 0^2 + 0^2 = 1
> $$

## Solution
It is very easy to solve. At the first beginning, i was thinking about how to identify if one number had already existed. So i use HashMap. But the solution time is not so gratifying.

One day later, I found such behavior of the sequence: if numbers are not happy, it will follow such sequence as follows:
$$
4, 16, 37, 58, 89, 145, 42, 20, 4, \ldots
$$

Beautiful!

## Code
``` c
bool isHappy(int n) {
    int sum;
    int temp;
    while (sum != 1) {
        sum = 0;
        temp = n;
        while (temp != 0) {
            int factor = temp / 10;
            int remainer = temp - 10 * factor;
            sum += (remainer * remainer);
            temp = factor;
        }
        n = sum;
        if (n == 4 || n == 16 || n == 37 || n == 58 || n == 89 || n == 145 || n == 42 || n == 20) {
            return false;
        }
    }
    return true;
}
```
