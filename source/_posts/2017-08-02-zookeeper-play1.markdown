---
layout: post
title:  "zookeeper-basic testing with Go client"
date:   2017-08-02 21:00:00 +1000
categories: zookeeper
---

testing env:
ubuntu 16 LTS
we just test stand-alone mode of zookeeper, in productin environment, at least 3 zookeepers are recommended.

## steps
install java
to make it simple, I just installed everything about java

```

sudo apt-get install default-jre
sudo apt-get install default-jdk
```


install zookeeper
```

sudo apt-get install zookeeper zookeeperd
```


start the server:
```

sudo service zookeeper start
```


we can also download the zookeeper tar from the official website
http://apache.org/dist/zookeeper/zookeeper-3.4.9/zookeeper-3.4.9.tar.gz

once download the tar file, directly run
```

tar zxvf zookeeper-3.4.9.tar.gz
```


The good thing is we can directly use 'sh' script in the sub 'bin' directory
./bin/zkCli.sh can be used to connect local zookeeper running server

## zookeeper quick start and reference
    the official 'wiki' provides a lot of useful information to help start with zookeeper,
    but following link is more simple to start with,
    https://www.tutorialspoint.com/zookeeper/index.htm


## golang zookeeper client testing

go-zookeeper is the opensource zookeeper client written in go, it has supported basic operations in zookeeper
https://github.com/wy3148/go-zookeeper.git

git clone https://github.com/wy3148/go-zookeeper.git

```
package main

import (
    "fmt"
    "time"

    "github.com/samuel/go-zookeeper/zk"
)

func main() {
    c, _, err := zk.Connect([]string{"192.168.0.22"}, time.Second) //*10)
    if err != nil {
        panic(err)
    }

    children, _, err := c.Children("/")

    if err != nil {
        panic(err)
    }

    fmt.Println("all sub znodes are:", children)

    //always set a new watch to znode
    for {
        children, stat, ch, err := c.GetW("/zookeeper")
        if err != nil {
            panic(err)
        }
        fmt.Printf("%+v %+v\n", string(children), stat)
        e := <-ch
        fmt.Printf("%+v\n", e)
    }
}
```








