---
title: using lettuce redis on java
date: 2020-08-12 21:01:21
tags:
---

## A general set/get command

```java
package com.mycompany.app;

import io.lettuce.core.*;
import io.lettuce.core.api.StatefulRedisConnection;
import io.lettuce.core.api.sync.RedisCommands;

/**
 * Hello world!
 */
public class App {
    public static void main(String[] args) {
        RedisClient redisClient = RedisClient.create("redis://localhost:6379/0");
        StatefulRedisConnection<String, String> connection = redisClient.connect();
        RedisCommands<String, String> syncCommands = connection.sync();
        syncCommands.set("key", "Hello, Redis!");
        connection.close();
        redisClient.shutdown();
    }
}




```



##publish/subscribe command


### setup the redis connection for pubsub

```java

		RedisClient redisClient = RedisClient.create("redis://localhost:6379/0");
        StatefulRedisPubSubConnection<String, String> pubsubConn = redisClient.connectPubSub();


        //add the listener to consume the message
        pubsubConn.addListener(new AppRedisPubSubListener());

        RedisPubSubAsyncCommands<String, String> async = pubsubConn.async();
        async.subscribe("CM");

```

###add the listener

RedisPubSubListener is the interface, we need to implement a listener class

```java


import io.lettuce.core.pubsub.RedisPubSubListener;

public class AppRedisPubSubListener implements RedisPubSubListener<String, String> {

    public void message(String channel, String message) {
        System.out.println("Got {} on channel {}" + message + ":" + channel);
    }

    public void message(String pattern, String channel, String message){

    }

    /**
     * Subscribed to a channel.
     *
     * @param channel Channel.
     * @param count Subscription count.
     */
    public void subscribed(String channel, long count){

    }

    /**
     * Subscribed to a pattern.
     *
     * @param pattern Pattern.
     * @param count Subscription count.
     */
    public void psubscribed(String pattern, long count){

    }

    /**
     * Unsubscribed from a channel.
     *
     * @param channel Channel.
     * @param count Subscription count.
     */
    public void unsubscribed(String channel, long count){

    }

    /**
     * Unsubscribed from a pattern.
     *
     * @param pattern Channel.
     * @param count Subscription count.
     */
    public void punsubscribed(String pattern, long count){

    }
}


```

To test the publish/subscribe command , the main thread shouldn't exit quickly,
int the main() we can add something code like this, to make applicaiton always in running state.

```
        while (true) {
            try {
                // thread to sleep for 1000 milliseconds
                Thread.sleep(1000);
            } catch (Exception e) {
                System.out.println(e);
                pubsubConn.close();
                redisClient.shutdown();
            }
        }

```