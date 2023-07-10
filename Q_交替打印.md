golang多线程打印

# 1. 两个协程交替打印

### 1.1. 一个chan实现

```go
package main

import (
	"fmt"
	"sync"
)

func main() {

	var wg sync.WaitGroup
	wg.Add(2)

	ch := make(chan int)

	go func() {
		defer wg.Done()
		for i := 0; i < 100; i++ {
			ch <- 1 // <-ch
			if i%2 == 0 {
				fmt.Println("thread1: ", i)
			}
		}
	}()

	go func() {
		defer wg.Done()
		for i := 0; i < 100; i++ {
			<-ch // ch <- 1
			if i%2 == 1 {
				fmt.Println("thread2: ", i)
			}
		}
	}()

	wg.Wait()
}
```

### 1.2. 两个chan实现

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var (
		wg  sync.WaitGroup
		ch1 = make(chan int)
		ch2 = make(chan int)
	)

	wg.Add(2)

	i := 0

	go func() {
		defer wg.Done()
		for {
			ch1 <- 1
			if i == 10 {
				<-ch2
				break
			}
			fmt.Println("[1]", i)
			i += 1
			<-ch2
		}
	}()

	go func() {
		defer wg.Done()
		for {
			ch2 <- 1
			if i == 10 {
				break
			}
			fmt.Println("[2]", i)
			i += 1
			<-ch1
		}
	}()

	<-ch1

	wg.Wait()
}
```

# 2. 三个协程顺序打印cat,dog,fish，打印10次

### 2.1. 三个chan实现

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var (
		wg  sync.WaitGroup
		ch1 = make(chan int)
		ch2 = make(chan int)
		ch3 = make(chan int)
	)

	wg.Add(3)

	go func() {
		defer wg.Done()
		for i := 1; i <= 10; i++ {
			ch1 <- 1
			fmt.Println("[1]: fish")
			<-ch2
		}
		ch1 <- 1 // 最后要加上这个，因为[3]最后会执行<-ch1
	}()

	go func() {
		defer wg.Done()
		for i := 1; i <= 10; i++ {
			ch2 <- 1
			fmt.Println("[2]: cat")
			<-ch3
		}
	}()

	go func() {
		defer wg.Done()
		for i := 1; i <= 10; i++ {
			ch3 <- 1
			fmt.Println("[3]: dog")
			<-ch1
		}
	}()

	<-ch1

	wg.Wait()
}
```
