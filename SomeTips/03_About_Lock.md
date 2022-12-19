# 关于锁

## 最佳实践

* 减少持有时间：减少临界区
* 优化锁的粒度
* 读写分离
* 使用原子操作

## 避免踩坑

### **不要拷贝mutex**

```go
func worker(m sync.Mutex) {
    m.Lock()
    defer m.Unlock()
    // do something
}

func main() {
    var mu sync.Mutex
    go worker(mu)
    go worker(mu)
    time.Sleep(time.Second)
}
```

无法正确执行。传值-->拷贝-->独立的状态&队列-->新的Mutex。因此上述两个worker加的不是同一个锁，业务语义不正确。

```go
type Mutex struct {
    state int32
    sema  uint32
}
```

正确的用法: 传指针

```go 
func worker(m *sync.Mutex) {...}

    go worker(&mu)
    go worker(&mu)
```

### 
