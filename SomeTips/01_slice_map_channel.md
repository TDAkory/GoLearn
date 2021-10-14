## slice vs array
* array 需指明长度，长度为常量且不可改变
* array 的长度为其类型中的组成部分
* array 在作为参数的时候会产生copy
* golang 所有的函数参数都是值传递 （Not Really Sure ？）

### growslice
```golang
// slice的len和cap分别是多少
var s []int     // 0,0

s = append(s, 0)    // 1,1

s = append(s, 1)
s = append(s, 2)    // 3, 4

for i := 3; i < 1025; i++ {     // 1025, 1280
    s = append(s, i) 
}
```
`growslice`处理slice扩容
* 当 `cap < 1024` 时，每次 `*2`
* 当 `cap >= 1024` 时，每次 `*1.25`
* 预分配内存可以提升性能，`s := make([]int, 0, 10000)` 替代 `s := []int`
* 直接使用idx赋值而非append赋值可以提升性能，机器码少

### 一些问题例子
```golang
func main() {
    var s []int
    for i := 0; i < 3; i++ {
        s = append(s, i)
    }
    modifySlice(s)
    fmt.Println(s)
}

// case 1
func modifySlice(s []int) {
    s[0] = 1024         // [1024, 1, 2] 值传递，但是slice底层是个指针，修改可见
}

// case 2
func modifySlice(s []int) {
    s = append(s, 2048)
    s[0] = 1024         // [1024, 1, 2] 值传递，slice下的len改变，外面不可见
}

// case 3
func modifySlice(s []int) {
    s = append(s, 2048)
    s = append(s, 4096)
    s[0] = 1024         // [0, 1, 2] 内部发生扩容，不在同一个内存地址了
}

// case 4
func modifySlice(s []int) {
    s[0] = 1024 
    s = append(s, 2048)
    s = append(s, 4096)  // [1024, 1, 2] 赋值发生在扩容之前，且外部len没有变
}
```
* 如果没有发生扩容，修改在原来的内存中
* 如果发生了扩容，修改会在新的内存中 

```golang
func main() {
    var s []int
    b, _ := json.Marshal(s)
    fmt.Println(string(b))  // null
}

func main() {
    s := []int{}
    b, _ := json.Marshal(s)
    fmt.Println(string(b))  // []
}

```
* 使用`[]Type{}`或者`make([]Type)`初始化后，slice部位`nil`
* 使用 `var x [] Type`后，slice为`nil`

### [Bounds Checking Elimination](https://go101.org/article/bounds-check-elimination.html)


## Map
* mao实际上的值是个指针，传的参数是指针
* 修改map是可见的
* map的key value都不可以取地址，随着map扩容地址会改变
* map存的是值，读写都会发生copy
* map删除key不会自动缩容

## Channel
* channel是有锁的
* channel 底层是个ringbuffer
* channel调用会触发调度
* 高并发、高性能编程不适合使用channel