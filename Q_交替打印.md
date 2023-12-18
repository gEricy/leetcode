golang多线程打印

# 1. 两个协程交替打印AB


```go
package main

import (
	"fmt"
	"sync"
)

func f() {
	ch1 := make(chan int)
	ch2 := make(chan int)

	var wg sync.WaitGroup
	wg.Add(2)

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			<-ch1
			fmt.Println("A", i*2)
			ch2 <- 1
		}
	}()

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			<-ch2
			fmt.Println("B", i*2+1)
			if i == 9 {
				break
			}
			ch1 <- 1
		}
	}()

	ch1 <- 1

	wg.Wait()
}

func main() {
	f()
}
```


# 2. 三个协程顺序打印cat,dog,fish，打印10次


```go
package main

import (
	"fmt"
	"sync"
)

func f() {
	ch1 := make(chan int)
	ch2 := make(chan int)
	ch3 := make(chan int)

	var wg sync.WaitGroup
	wg.Add(3)

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			<-ch1
			fmt.Println("cat")
			ch2 <- 1
		}
	}()

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			<-ch2
			fmt.Println("fish")
			ch3 <- 1
		}
	}()

	go func() {
		defer wg.Done()
		for i := 0; i < 10; i++ {
			<-ch3
			fmt.Println("dog")
			if i == 9 {
				break
			}
			ch1 <- 1
		}
	}()

	ch1 <- 1

	wg.Wait()
}

func main() {
	f()
}
```
