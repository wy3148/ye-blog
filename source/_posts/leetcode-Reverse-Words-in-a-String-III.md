---
title: leetcode-Reverse-Words-in-a-String-III
date: 2017-08-23 22:14:53
tags: java
categories: leetcode
---


Given a string, you need to reverse the order of characters in each word within a sentence while still preserving whitespace and initial word order.

```

Example 1:
Input: "Let's take LeetCode contest"
Output: "s'teL ekat edoCteeL tsetnoc"
Note: In the string, each word is separated by single space and there will not be any extra space in the string.
```

```java
class Solution {
    
    public String reverseSingle(String c){
        char[] str = c.toCharArray();
        int i = 0;
        char tmp;
        
        while(i < c.length()/2){
            tmp = str[i];
            str[i] = str[c.length()-1-i];
            str[c.length()-1-i] = tmp;
            i++;
        }
        return new String(str);
    }
    
    public String reverseWords(String s) {
        
        String[] m = s.split(" ");
        
        int i;
        String res = "";
        
        for (i = 0; i < m.length; i++){
            
            if (res.length() == 0){
                res += reverseSingle(m[i]);
            }else{
                res = res + " " + reverseSingle(m[i]);
            }
        }
        return res;
    }
}
```

someone post another way using the natural StringBuilder class,
it has 'reverse' method,

```java

    public String reverseWords(String s) {
        String[] str = s.split(" ");
        for (int i = 0; i < str.length; i++) str[i] = new StringBuilder(str[i]).reverse().toString();
        StringBuilder result = new StringBuilder();
        for (String st : str) result.append(st + " ");
        return result.toString().trim();
    } 
```


