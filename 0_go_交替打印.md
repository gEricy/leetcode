golang多线程打印

# 1. 两个协程交替打印AB


```go
package main

import "sync"

func main() {
	wg := sync.WaitGroup{}

	ch1 := make(chan int)
	ch2 := make(chan int)

	wg.Add(2)

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			println("[1] A")
			ch2 <- 1
			<-ch1
		}
	}()

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			<-ch2
			println("[2] B")
			ch1 <- 1
		}
	}()

	wg.Wait()
}

```


# 2. 三个协程顺序打印cat,dog,fish，打印10次


```go
package main

import "sync"

func main() {
	wg := sync.WaitGroup{}

	ch1 := make(chan int)
	ch2 := make(chan int)
	ch3 := make(chan int)

	wg.Add(3)

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			println("[1] A")
			ch2 <- 1
			<-ch1
		}
	}()

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			<-ch2
			println("[2] B")
			ch3 <- 1
		}
	}()

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			<-ch3
			println("[3] C")
			ch1 <- 1
		}
	}()

	wg.Wait()
}
```
