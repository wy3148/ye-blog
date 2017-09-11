---
title: try sync.Map in golang 1.9
date: 2017-09-01 10:08:51
tags: golang
categories: golang
---

Golang 1.9 new version support safe Map now, details can be found,

https://golang.org/pkg/sync/#Map.Load


Here is sample code, test filename is 'sync_map.go'


```golang

package main

import (
	"fmt"
	"sync"
	"time"
)

func writeProc(m *sync.Map, c chan int) {

    go func() {

        i := 0
        for {
            v, ok := m.Load("ok1")

            if ok {
                fmt.Println("get the ok 1")
                fmt.Println(v)
                c <- 1
                return
            }

            fmt.Printf("does not get the key element, sleep for one second, counting: %d\n", i)
            i++
            time.Sleep(1000 * time.Millisecond)
        }
    }()
}

func main() {

	m := new(sync.Map)

	c := make(chan int)

	writeProc(m, c)

	time.Sleep(10 * time.Second)
	m.Store("ok1", "goood!!!")
	<-c
}
```

output example:
```
go run sync_map.go 
does not get the key element, sleep for one second, counting: 0
does not get the key element, sleep for one second, counting: 1
does not get the key element, sleep for one second, counting: 2
does not get the key element, sleep for one second, counting: 3
does not get the key element, sleep for one second, counting: 4
does not get the key element, sleep for one second, counting: 5
does not get the key element, sleep for one second, counting: 6
does not get the key element, sleep for one second, counting: 7
does not get the key element, sleep for one second, counting: 8
does not get the key element, sleep for one second, counting: 9
get the ok 1
goood!!!

```