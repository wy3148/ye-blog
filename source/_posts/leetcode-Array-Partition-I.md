---
title: leetcode-Array-Partition-I
date: 2017-08-23 21:37:10
tags: java
categories: leetcode binary tree
---


Given an array of 2n integers, your task is to group these integers into n pairs of integer, say (a1, b1), (a2, b2), ..., (an, bn) which makes sum of min(ai, bi) for all i from 1 to n as large as possible.

Example 1:
Input: [1,4,3,2]

Output: 4
Explanation: n is 2, and the maximum sum of pairs is 4 = min(1, 2) + min(3, 4).
Note:
n is a positive integer, which is in the range of [1, 10000].
All the integers in the array will be in the range of [-10000, 10000].


Answer:
we should try to get the maxium element in each pair, so firstly we use quick sort to 
sort all integer elements, then caculate the sum pair by pair

Point: This topic is mainly about the quick sort algorithm.


```java

class Solution {
    
    int partition(int arr[], int low, int high)
    {
        int pivot = arr[high]; 
        int i = (low-1); // index of smaller element
        for (int j=low; j<high; j++)
        {
            // If current element is smaller than or
            // equal to pivot
            if (arr[j] <= pivot)
            {
                i++;
 
                // swap arr[i] and arr[j]
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }
 
        // swap arr[i+1] and arr[high] (or pivot)
        int temp = arr[i+1];
        arr[i+1] = arr[high];
        arr[high] = temp;
 
        return i+1;
    }
 
 
    /* The main function that implements QuickSort()
      arr[] --> Array to be sorted,
      low  --> Starting index,
      high  --> Ending index */
    void sort(int arr[], int low, int high)
    {
        if (low < high)
        {
            /* pi is partitioning index, arr[pi] is 
              now at right place */
            int pi = partition(arr, low, high);
 
            // Recursively sort elements before
            // partition and after partition
            sort(arr, low, pi-1);
            sort(arr, pi+1, high);
        }
    }
    

    public int arrayPairSum(int[] nums) {
        
        if (nums.length < 2) return 0;
    
        sort(nums,0,nums.length-1);
        
        int sum = 0;
        int i = 0;
        
        while(i < nums.length){
            sum = sum + nums[i];
            i = i + 2;
        }
        
        return sum;
    }
}
```

Actually, java Array class naturally has 'sort' method

```

static void	sort(int[] a, int fromIndex, int toIndex)
Sorts the specified range of the array into ascending order.
```

so someone also post following code

```java

public class Solution {
    public int arrayPairSum(int[] nums) {
        Arrays.sort(nums);
        int result = 0;
        for (int i = 0; i < nums.length; i += 2) {
            result += nums[i];
        }
        return result;
    }
}
```
which is much more simple.
