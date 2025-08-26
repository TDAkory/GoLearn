# 如何实现调用一次

在 Go 语言中，可以使用 `init()` 函数和 `sync.Once` 类型来实现全局变量和全局函数只被初始化或调用一次。

## 使用 `init()` 函数初始化全局变量

`init()` 函数是 Go 语言中的特殊函数，它会在包被加载时自动执行，并且只会执行一次。可以利用这个特性来初始化全局变量。

```go
package main

import "fmt"

// 全局变量
var globalVar int

func init() {
    globalVar = 42
    fmt.Println("Global variable initialized:", globalVar)
}

func main() {
    fmt.Println("Global variable in main:", globalVar)
}
```

## 使用 `sync.Once` 确保全局函数只调用一次

`sync.Once` 提供了 `Do` 方法，可以确保其参数函数只被执行一次。

```go
package main

import (
    "sync"
    "fmt"
)

var (
    mu sync.Mutex
    once sync.Once
)

func main() {
    // 模拟多次调用
    for i := 0; i < 5; i++ {
        process()
    }
}

func process() {
    once.Do(func() {
        mu.Lock()
        defer mu.Unlock()
        // 只执行一次的初始化逻辑
        fmt.Println("Initializing...")
    })

    // 正常业务逻辑
    fmt.Println("Processing...")
}
```

## 使用 `sync.Once` 初始化全局变量

同样，`sync.Once` 也可以用来确保全局变量只被初始化一次，尤其是在需要并发安全的场景下。

```go
package main

import (
    "sync"
    "fmt"
)

var (
    globalVar int
    once      sync.Once
)

func main() {
    // 模拟多次初始化
    for i := 0; i < 5; i++ {
        initGlobal()
        fmt.Println("Global variable value:", globalVar)
    }
}

func initGlobal() {
    once.Do(func() {
        globalVar = 42
        fmt.Println("Global variable initialized:", globalVar)
    })
}
```

## 注意事项

- **`init()` 函数**：每个包可以有多个 `init()` 函数，它们会按照一定的顺序（通常是按照文件中声明的顺序）自动执行。`init()` 函数适用于在包加载时进行初始化操作。
- **`sync.Once`**：适用于需要在程序运行期间确保某段代码只执行一次的场景，尤其是在并发环境下。它提供了更灵活的控制，并且可以在程序的任何位置使用。
