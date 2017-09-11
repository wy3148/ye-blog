---
title: leetcode-Number-Complement
date: 2017-08-24 22:21:14
tags: java
categories: leetcode
---


Given a positive integer, output its complement number. The complement strategy is to flip the bits of its binary representation.

Note:
The given integer is guaranteed to fit within the range of a 32-bit signed integer.
You could assume no leading zero bit in the integer’s binary representation.

```

Example 1:
Input: 5
Output: 2
Explanation: The binary representation of 5 is 101 (no leading zero bits), and its complement is 010. So you need to output 2.
```


```
class Solution {
    public int findComplement(int num) {
        long i;
        for(i=1;i<=num;i*=2) num^=i; 
        return num;  
    }
}
```

