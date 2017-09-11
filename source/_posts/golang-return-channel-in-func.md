---
title: golang-return channel vs retufn <- channel 
date: 2017-09-04 09:43:02
tags: golang
---


https://stackoverflow.com/questions/31920353/whats-the-difference-between-chan-and-chan-as-a-function-return-type

in golang, some functions can return 'channel', and some functions return channel with prefix:' <- '



Golang newbie here.

Is there a functional difference between

```java

func randomNumberGenerator() <-chan int {
and

func randomNumberGenerator() chan int {
I've tried using both and they seem to work fine for me.
```

I've seen the former used by Rob Pike (one of Go creators) in his Go Concurrency Patterns talk at Google IO 2012. I've also seen it used in Go's official website. Why add 2 extra characters ("<-") when you can omit it? I've tried looking for the difference on the web, but couldn't find it.





Both will work indeed. But one will be more constraining. The form with the arrow pointing away from the chan keyword means that the returned channel will only be able to be pulled from by client code. No pushing allowed : the pushing will be done by the random number generator function. Conversely, there's a third form with the arrow pointing towards chan, that makes said channel write-only to clients.

chan   // read-write
<-chan // read only
chan<- // write only
These added constraints can improve the expression of intent and tighten the type system : attempts to force stuff into a read-only channel will leave you with a compilation error, and so will attempts to read from a write-only channel. These constraints can be expressed in the return type, but they can also be part of the parameter signature. Like in :

func log(<-chan string) { ...
There you can know, just by the signature, that the log function will consume data from the channel, and not send any to it.


code sample

```java


func shopping(num int) <-chan int {

	res := make(chan int, 1)

	go func() {
		defer close(res)
		time.Sleep(5 * time.Second)
		res <- 100
	}()

	return res
}

func main() {

	v := shopping(5)
	v <- 100
}
```

It will generate the compile error
invalid operation: v <- 100 (send to receive-only type <-chan int)

