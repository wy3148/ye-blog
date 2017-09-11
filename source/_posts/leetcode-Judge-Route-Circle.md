---
title: leetcode-Judge-Route-Circle
date: 2017-08-23 15:25:12
tags: java
categories: leetcode
---

Description
Initially, there is a Robot at position (0, 0). Given a sequence of its moves, judge if this robot makes a circle, which means it moves back to the original place.

The move sequence is represented by a string. And each move is represent by a character. The valid robot moves are R (Right), L (Left), U (Up) and D (down). The output should be true or false representing whether the robot makes a circle.

Example 1:
Input: "UD"
Output: true
Example 2:
Input: "LL"
Output: false

```java

class Solution {
    public boolean judgeCircle(String moves) {
        
        int x = 0;
        int y = 0;
        
        //R,L,U,D
        
        int i;
        
        for ( i = 0; i < moves.length(); i++){
            
            if (moves.charAt(i) == 'R'){
                x++;
            }else if (moves.charAt(i) == 'L'){
                x--;
            }else if (moves.charAt(i) == 'U'){
                y++;
            }else if (moves.charAt(i) == 'D'){
                y--;
            }
        }
        
        if (x == 0 && y == 0) return true;
        
        return false;
    }
}

```