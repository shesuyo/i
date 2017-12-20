
开发中有这样一个需求：因为这个请求的时间会比较长，所以在处理的时候的时候希望一次只处理一个请求，其他的请求都返回稍后再试。（当然其他的方案可以放入到队列里面，然后先全部返回成功，然后再从队列里面一个一个处理。）

我就想能不能用超时解决这个问题，有一个锁锁住了，然后开一个select 拿锁，如果一毫秒拿不到锁就超时。这个时候我就想如果一个锁被两次unlock会发生什么事情。
```
package main

import (
	"fmt"
	"sync"
	"time"
)

var (
	mu = sync.Mutex{}
)

func main() {
	for {
		mu.Unlock()
		fmt.Println("job")
		time.Sleep(1 * time.Second)
	}
}

func Do() {
	fmt.Println("s do")
	mu.Lock()
	time.Sleep(5 * time.Second)
	defer mu.Unlock()
	fmt.Println("e do")
}
```
结果居然panic了，妥妥的。
```
panic: sync: unlock of unlocked mutex

goroutine 1 [running]:
sync.(*Mutex).Unlock(0x558808)
	c:/go/src/sync/mutex.go:184 +0xc8
main.main()
	E:/gopath/src/ekt.com/labs/089-safe-lock/main.go:15 +0x34
```

然后测试select，发现之间select的想法也不行。
```
.\main.go:16:15: select case must be receive, send or assign recv
```

然后我就想到了使用channel来代替锁，这样就可以用select了。
```
func main() {
	c := make(chan struct{}, 1)

	for {
		select {
		case <-time.After(1 * time.Nanosecond):
			fmt.Println("NotGot")
		case c <- struct{}{}:
			fmt.Println("GOT")
			<-c
		}
		time.Sleep(100 * time.Millisecond)
	}
}
```
结果也很满意
```
GOT
GOT
GOT
GOT
GOT
GOT
GOT
GOT
GOT
```


接着测试并发
```
func main() {
	c := make(chan struct{}, 1)
	go func() {
		for {
			select {
			case <-time.After(1 * time.Nanosecond):
				fmt.Println("NotGot")
				time.Sleep(100 * time.Millisecond)
			case c <- struct{}{}:
				fmt.Println("1 GOT")
				time.Sleep(50 * time.Millisecond)
				<-c
			}
		}
	}()
	go func() {
		for {
			select {
			case <-time.After(1 * time.Nanosecond):
				fmt.Println("NotGot")
				time.Sleep(100 * time.Millisecond)
			case c <- struct{}{}:
				fmt.Println("2 GOT")
				time.Sleep(50 * time.Millisecond)
				<-c
			}
		}
	}()
	time.Sleep(1 * time.Hour)
}
```

```
1 GOT
NotGot
1 GOT
1 GOT
1 GOT
NotGot
1 GOT
2 GOT
NotGot
2 GOT
2 GOT
NotGot
```
