---
title: golang-context examples
date: 2017-09-04 11:21:54
tags: golang
---


#context with cancel
parent go routine cancel the child goroutine,
when parent routine stops to consume more messages, it call context.Cancel() function
to signal the child routine

```golang
package main

import (
	"context"
	"fmt"
)

func main() {
	// gen generates integers in a separate goroutine and
	// sends them to the returned channel.
	// The callers of gen need to cancel the context once
	// they are done consuming generated integers not to leak
	// the internal goroutine started by gen.
	gen := func(ctx context.Context) <-chan int {
		dst := make(chan int)
		n := 1
		go func() {
			for {
				select {
				case <-ctx.Done():
					return // returning not to leak the goroutine
				case dst <- n:
					n++
				}
			}
		}()
		return dst
	}

	ctx, cancel := context.WithCancel(context.Background())
	defer cancel() // cancel when we are finished consuming integers

	for n := range gen(ctx) {
		fmt.Println(n)
		if n == 5 {
			break
		}
	}
}

```


#context with value

we can pass some value from routines to routines, about the 'key', it's not suggested using
string data type directly

```golang

package main

import (
	"context"
	"fmt"
	// "net"
	"net/http"
	"time"
)

type TracId string

func moreTime(c context.Context) <-chan int {
	v := c.Value(TracId("src"))
	fmt.Println("remote src is:", v.(string))

	res := make(chan int, 1)
	time.Sleep(1 * time.Second)
	res <- 1
	return res
}

func foo(w http.ResponseWriter, r *http.Request) {
	c := r.Context()
	newC := context.WithValue(c, TracId("src"), r.RemoteAddr)
	go moreTime(newC)
}

func main() {

	http.HandleFunc("/foo", foo)
	http.ListenAndServe(":8081", nil)
}
```



#context with timeout

```golang

package main

import (
	"context"
	"fmt"
	// "net"
	"net/http"
	"time"
)

type TracId string

func moreTime(c context.Context) <-chan string {

	res := make(chan string, 1)
	go func() {
		defer close(res)
		for {
			select {
			case <-time.After(1 * time.Second):
				fmt.Println("overslept")
				res <- "working\n"
			case <-c.Done():
				fmt.Println(c.Err()) // prints "context deadline exceeded"
				res <- "timeout\n"
				return
			}
		}
	}()

	return res
}

func foo(w http.ResponseWriter, r *http.Request) {
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()

	res := moreTime(ctx)

	for v := range res {
		w.Write([]byte(v))
	}
}

func main() {

	http.HandleFunc("/foo", foo)
	http.ListenAndServe(":8081", nil)
}
```

when we don't want to the response forever, we can set a timeout in this way,
we need to check the 'error' response of client.Do() function
```golang

	ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
	defer cancel()

	client := new(http.Client)
	req, _ := http.NewRequest("GET", "http://192.168.2.59:8081/foo", nil)
	copyReq := req.WithContext(ctx)

	if err != nil {
		fmt.Println(err)
	} else {
		b := make([]byte, 1024)
		ress.Body.Read(b)
		fmt.Println(string(b))
	}

```
