---
title: binary search algorithm--different ways in coding
date: 2020-04-20 20:40:33
tags: algorithm
---


This is a general summary about binary search implemented in different ways.
We try to find the difference among these different ways.


##Find the exact target

find a target in a sorted array

``` c++


    bool search(vector<int>& nums1, int v){
        int l = 0;
        int r = nums1.size() -1;
        
        while(l <= r){
            int mid = ( l + r) /2;
            if (nums1[mid] == v) return true;
            if (nums1[mid] > v){
                r = mid -1;
            }else{
                l = mid + 1;
            }
        }
        return false;
    }

    //in the coding, while condition is l <= r

```

suppose we have an array that is 

[a, b, c]

l = 0, r = 2 at the beginning, 
mid =1;  if target is b, we have found the target;
if target is a, r = mid - 1 = 0;  so now l = r ; we can find that target;
if target is b, l = mid + 1=  1; and now l = r; we can find that target.

```c++
    bool searchII(vector<int>& nums1, int v){
        int l = 0;
        int r = nums1.size() -1;
        
        while(l + 1 < r){
            int mid = ( l + r) /2;
            if (nums1[mid] == v) return true;
            if (nums1[mid] > v){
                r = mid;
            }else{
                l = mid;
            }
        }
        return nums1[l] == v || nums1[r] == v;
    }
```
in this way, before the next time of loop, l or r is always set to 'mid' value;
only the while condition finally cause two adjancent elements are left in array;
that's why we finally need to verify 
```c++
return nums1[l] == v || nums1[r] == v;
```

the second way to implement binary search is safer way which avoid the possibile overflow or panic
in array iteration.



##find the first position where element is bigger or equal to  target


```c++
public int firstGreatOrEqual(int[] a, int n, int key){
		//n + 1 个数
		int low = 0;
		int high = n;
		int mid = 0;
		while(low <= high) {
			mid = low + ((high-low) >> 1);
			if(key <= a[mid]) {
				high = mid - 1;
			} else {
				low = mid + 1;
			}
		}
		return low <= n ? low : -1;
	}
```


##find the first element where element is bigger than target

```c++
int low = 0;
		int high = n;
		int mid = 0;
		while(low <= high) {
			mid = low + ((high-low) >> 1);
			if(key < a[mid]) {
				high = mid - 1;
			} else {
				low = mid + 1;
			}
		}
		return low <= n ? low : -1;
```

the difference part is  
```c++
if (key <= a[mid]) and if (key < a[mid])
```
so we try to find the location what 'low' position element is bigger than targt value

There are more questions such as 
find the maximum position of the element which is equal to target
or 
find the minimum position of the element which is eqaul to target
