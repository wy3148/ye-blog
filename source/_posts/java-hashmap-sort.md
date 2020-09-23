---
title: sort in hashmap
date: 2020-09-18 21:46:38
tags:
---



##Hashmap in java, specifiy the Comparator


```java

	class ComparatorImpl implements Comparator<Map.Entry<Character,Integer>>{
		public int compare(Map.Entry<Character,Integer> a ,Map.Entry<Character,Integer> b){
			return b.getValue() - a.getValue();
		}
	}

    public String frequencySort(String s) {
        StringBuilder ret = new StringBuilder();
        HashMap<Character, Integer> map = new HashMap<>();

        for(int i = 0; i < s.length(); i++){
            map.put(s.charAt(i),  map.getOrDefault(s.charAt(i),0) + 1);
        }

        ArrayList<Map.Entry<Character,Integer>> list = new ArrayList<>(map.entrySet());
        Collections.sort(list, new ComparatorImpl());

        for(Map.Entry<Character,Integer> c: list){
            for(int i =0; i < c.getValue(); i++){
                ret.append(c.getKey());
            }
        }
        return ret.toString();
    }

```

This is to define the sort algorithm,
```java
	class ComparatorImpl implements Comparator<Map.Entry<Character,Integer>>{
		public int compare(Map.Entry<Character,Integer> a ,Map.Entry<Character,Integer> b){
			return b.getValue() - a.getValue();
		}
	}

```


1 convert the hash Entry to array list, and sort.
2 there is other way, such as TreeSet

Reference:
https://www.baeldung.com/java-hashmap-sort