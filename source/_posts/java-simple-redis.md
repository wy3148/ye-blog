---
title: java_simple_redis
date: 2020-07-27 23:20:34
tags: java
categories: java
---



#using java redis opensource


```java
  <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
  <dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>3.2.0</version>
  </dependency>
 ```


 ```java

public class App 
{
	
    public static void main( String[] args )
    {
        Jedis client = new Jedis("localhost");
        ListNode n = new ListNode(1);

        for(int i = 0; i < 1000; i++){
            client.set("key" + String.valueOf(i), String.valueOf(i));
        }

        client.close();
    }
}

 ```
