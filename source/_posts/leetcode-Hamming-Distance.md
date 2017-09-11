---
title: leetcode-Hamming Distance
date: 2017-08-22 16:22:20
tags: java
categories: leetcode
---


The Hamming distance between two integers is the number of positions at which the corresponding bits are different.

Given two integers x and y, calculate the Hamming distance.

Note:
0 ≤ x, y < 231.


```java
class Solution {
    public int hammingDistance(int x, int y) {
        int i =  32;
        int count = 0;
        int tmp;

        for( i = 0 ; i < 32; i++) {
            
            tmp = 1 << i;

            if ( (x & tmp) != (y & tmp)) {
                count++;
            } 
        }
        return count;      
    }
}
```