
---
title: java use maven
date: 2020-07-27 21:55:53
tags: java
categories: java
---


maven


# maven local cache
local cache

$Default_user_dir/.m2/repository

reference:
https://maven.apache.org/index.html


# maven pom.xml



maven.compiler plugin is used to specifi the jave SDK version,
in my local mac, after mvn init , the default version which is 1.7
which might be too old.



```java 

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>

```

User should validate the local java sdk, run 'java -version', for example,

```java
java -version
openjdk version "14.0.2" 2020-07-14
OpenJDK Runtime Environment (build 14.0.2+12-46)
OpenJDK 64-Bit Server VM (build 14.0.2+12-46, mixed mode, sharing)
```

#java sdk version is important


for example, java sdk 1.7 only has constructor in priority queue

```java
PriorityQueue(int initialCapacity, Comparator<? super E> comparator)
```
while java sdk1.8 has a new contrustor

```java
PriorityQueue(Comparator<? super E> comparator)
```
java SDK will use a default size when create a new PriorityQueue






