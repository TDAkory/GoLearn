# [Go Spec](https://go.dev/ref/spec#Package_initialization) & General Knowing

## Package Initialization

总体而言:  包级变量初始化 ->  init() -> main()
init 函数特点: 

- 无参数、无返回值、先于 main 执行
- 每个包可有多个文件定义 init, 一个文件可以定义多个 init
- 同一个包的 init 执行顺序，没有明确定义； 不同包的 init 函数 按导入依赖推导顺序来执行；

runtime 初始化规则:

1. 递归初始化导入包
2. package block 的变量进行表达式计算 和 赋值初始化
3. 执行包内定义的 init 函数

>一个包被导入多次，也只会执行一次初始化

```go
package main

import "fmt"

var (
    a = c +1
    c = testA()
    b = 2
)

func testA() int {
    fmt.Println("enter testA")
    b = 3
    return b + 2
}

func init() {
    fmt.Println("enter init")
}

func main() {
    fmt.Println("enter main")
    fmt.Println(a, b, c)
}
```

输出：
enter testA
enter init
enter main
6 3 5

## Panic & Recovery

```go
func panic(interface{})
func recover() interface{}
```

一些总结: 

- recover 只有放在 defer 中才有意义，其他情况都会返回 nil 
- panic 未被捕获时，会随函数调用栈层层传递；
- 在执行到 defer 函数的 recover 前，还是可以 reproduce panic 的
- panic 会中断现有执行流程，直接进入 defer functions 的 LIFO 调用顺序中，直至 panic 被捕获，或者程序异常退出

```go 
package panic_recovery

import "fmt"

func A() {
	defer func() {
		fmt.Println("A.Func() 1")
	}()

	defer func() {
		fmt.Println("A.Func() 2")
		panic("abort in defer function")
	}()

	B()
	fmt.Println("should not be here")
}

func B() {
	defer func() {
		fmt.Println("defer func in B")
	}()
	panic("panic in B")
}

func C() {
	defer func() {
		if x:=recover(); x!=nil {
			fmt.Println(x)
		}
	}()
	A()
}

####
import "testing"

func TestC(t *testing.T) {
    C()
}

// === RUN   TestC
// defer func in B
// A.Func() 2
// A.Func() 1
// abort in defer function
```

## [Context](https://pkg.go.dev/context)

Package context defines the Context type, which carries deadlines, cancellation signals, and other request-scoped values across API boundaries and between processes.

Incoming requests to a server should create a Context, and outgoing calls to servers should accept a Context. The chain of function calls between them must propagate the Context, optionally replacing it with a derived Context created using `WithCancel`, `WithDeadline`, `WithTimeout`, or `WithValue`. When a Context is canceled, all Contexts derived from it are also canceled.

> [剖析 Golang Context：从使用场景到源码分析](https://xie.infoq.cn/article/3e18dd6d335d1a6ab552a88e8)

## [WaitGroup](https://pkg.go.dev/sync#WaitGroup)

A WaitGroup waits for a collection of goroutines to finish. The main goroutine calls Add to set the number of goroutines to wait for. Then each of the goroutines runs and calls Done when finished. At the same time, Wait can be used to block until all goroutines have finished.

A WaitGroup must not be copied after first use.

```go
package main

import (
    "sync"
)

type httpPkg struct{}

func (httpPkg) Get(url string) {}

var http httpPkg

func main() {
    var wg sync.WaitGroup
    var urls = []string{
    	"http://www.golang.org/",
    	"http://www.google.com/",
    	"http://www.somestupidname.com/",
    }
    for _, url := range urls {
    	// Increment the WaitGroup counter.
    	wg.Add(1)
    	// Launch a goroutine to fetch the URL.
    	go func(url string) {
    		// Decrement the counter when the goroutine completes.
    		defer wg.Done()
    		// Fetch the URL.
    		http.Get(url)
    	}(url)
    }
    // Wait for all HTTP fetches to complete.
    wg.Wait()
}
```

## interface

> [理解 Go interface 的 5 个关键点](https://sanyuesha.com/2017/07/22/how-to-understand-go-interface/)

## [sync.Pool](https://pkg.go.dev/sync#Pool)

## [unsafe](https://pkg.go.dev/unsafe)

Package unsafe contains operations that step around the type safety of Go programs.

Packages that import unsafe may be non-portable and are not protected by the Go 1 compatibility guidelines.

### [unsafe.Pointer](https://pkg.go.dev/unsafe#Pointer)

- A pointer value of any type can be converted to a Pointer.
- A Pointer can be converted to a pointer value of any type.
- A uintptr can be converted to a Pointer.
- A Pointer can be converted to a uintptr.

## [reflect](https://draveness.me/golang/docs/part2-foundation/ch04-basic/golang-reflect/)

## [defer](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-defer/)

## [runtime]

## [time]

## [gopkg/sync]

## [go test]