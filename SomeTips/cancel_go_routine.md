在 Go 中，可以使用以下几种方法优雅地等待协程退出：

### 1. 使用 `sync.WaitGroup`

`sync.WaitGroup` 是 Go 标准库中用于等待一组协程完成的工具。它提供了一个简单的方式，可以在主协程中等待所有子协程执行完毕。

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

func worker(id int, wg *sync.WaitGroup) {
    defer wg.Done() // 在协程结束时通知 WaitGroup

    fmt.Printf("Worker %d starting\n", id)

    time.Sleep(time.Second) // 模拟工作
    fmt.Printf("Worker %d done\n", id)
}

func main() {
    var wg sync.WaitGroup

    // 启动多个协程
    for i := 1; i <= 5; i++ {
        wg.Add(1) // 增加 WaitGroup 的计数
        go worker(i, &wg)
    }

    // 等待所有协程完成
    wg.Wait()
    fmt.Println("All workers completed")
}
```

### 2. 使用 `context.Context` 传递取消信号

`context.Context` 提供了一种优雅的方式来取消协程或通知协程退出。这在需要提前终止任务时非常有用。

```go
package main

import (
    "context"
    "fmt"
    "time"
)

func worker(ctx context.Context, id int) {
    for {
        select {
        case <-ctx.Done():
            fmt.Printf("Worker %d received cancel signal, exiting\n", id)
            return
        default:
            fmt.Printf("Worker %d is working\n", id)
            time.Sleep(500 * time.Millisecond)
        }
    }
}

func main() {
    ctx, cancel := context.WithCancel(context.Background())

    // 启动多个协程
    for i := 1; i <= 3; i++ {
        go worker(ctx, i)
    }

    // 模拟主协程工作
    time.Sleep(2 * time.Second)

    // 发送取消信号
    cancel()
    fmt.Println("Main goroutine canceling workers")

    // 继续其他工作
    time.Sleep(1 * time.Second)
    fmt.Println("Main goroutine exiting")
}
```

### 3. 使用 `sync.Cond` 实现自定义通知机制

`sync.Cond` 提供了一种更灵活的通知机制，可以用于复杂的同步场景。

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

func main() {
    var (
        mu     sync.Mutex
        cond   = sync.NewCond(&mu)
        exitCh = make(chan int)
    )

    // 启动多个协程
    for i := 1; i <= 3; i++ {
        go func(id int) {
            mu.Lock()
            for {
                cond.Wait() // 等待通知
                fmt.Printf("Worker %d received signal, exiting\n", id)
                mu.Unlock()
                exitCh <- id
                return
            }
        }(i)
    }

    // 模拟主协程工作
    time.Sleep(1 * time.Second)

    // 发送通知
    mu.Lock()
    cond.Broadcast() // 通知所有协程
    mu.Unlock()

    // 等待所有协程退出
    for i := 0; i < 3; i++ {
        <-exitCh
    }
    fmt.Println("All workers exited")
}
```

### 4. 使用通道关闭机制

通道（channel）可以用于协程间的通信和同步。通过关闭通道，可以通知协程退出。

```go
package main

import (
    "fmt"
    "time"
)

func worker(tasks <-chan int, results chan<- int) {
    for task := range tasks {
        fmt.Printf("Processing task %d\n", task)
        time.Sleep(300 * time.Millisecond)
        results <- task * 2
    }
    fmt.Println("Worker exiting")
}

func main() {
    tasks := make(chan int, 5)
    results := make(chan int, 5)

    // 启动多个协程
    for i := 0; i < 3; i++ {
        go worker(tasks, results)
    }

    // 发送任务
    for i := 1; i <= 5; i++ {
        tasks <- i
    }
    close(tasks) // 关闭任务通道，通知协程退出

    // 收集结果
    for i := 0; i < 5; i++ {
        <-results
    }
    fmt.Println("All tasks completed")
}
```

### 总结

- **`sync.WaitGroup`**：适用于等待一组协程完成，简单易用。
- **`context.Context`**：适用于需要优雅取消或超时控制的场景。
- **`sync.Cond`**：适用于复杂的同步场景，提供灵活的通知机制。
- **通道关闭机制**：适用于通过通道通信和同步的场景，关闭通道可以通知协程退出。

选择合适的方法取决于具体需求和场景。