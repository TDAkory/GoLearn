# 1. 流程控制

- [1. 流程控制](#1-流程控制)
  - [1.1. 条件语句](#11-条件语句)
  - [1.2. 分支语句](#12-分支语句)
    - [1.2.1. 合并分支](#121-合并分支)
  - [1.3. 循环语句](#13-循环语句)
  - [1.4. 跳转语句](#14-跳转语句)

- 条件语句：用于条件判断，对应的关键字有 if、else 和 else if；
- 分支语句：用于分支选择，对应的关键字有 switch、case 和 select（用于通道，后面介绍协程时会提到）；
- 循环语句：用于循环迭代，对应的关键字有 for 和 range；
- 跳转语句：用于代码跳转，对应的关键字有 goto；

## 1.1. 条件语句

```go
// if
if condition { 
    // do something 
}
// if...else...
if condition { 
    // do something 
} else {
    // do something 
}
// if...else if...else...
if condition1 { 
    // do something 
} else if condition2 {
    // do something else 
} else {
    // catch-all or default 
}
```

- 条件语句不需要使用圆括号将条件包含起来 ()；
- 无论语句体内有几条语句，花括号 {} 都是必须存在的；
- 左花括号 { 必须与 if 或者 else 处于同一行；
- 在 if 之后，条件语句之前，可以添加变量初始化语句，使用 ; 间隔，比如上述代码可以这么写 `if score := 100; score > 90 {`

## 1.2. 分支语句

```go
switch var1 {
    case val1:
        ... 
    case val2:
        ... 
    default:
        ...
}
```

### 1.2.1. 合并分支

```go
score := 100
switch score {
case 90, 100:       // 我们可以用逗号分隔不同的分支条件来达到合并分支语句的目的
    fmt.Println("Grade: A")
case 80:
    fmt.Println("Grade: B")
case 70:
    fmt.Println("Grade: C")
case 60:            // case 60 这个分支语句在 Go 语言中会被认为是空语句
case 65:
    fmt.Println("Grade: D")
default:
    fmt.Println("Grade: F")
}
```

Go 分支语句中比较有意思的一点，那就是不需要显式通过 break 语句退出某个分支，上一个分支语句代码会在下一个 case 语句出现之前自动退出，如果你想要继续执行后续分支代码，可以通过一个 fallthrough 语句来声明。

- 和条件语句一样，左花括号 { 必须与 switch 处于同一行；
- 单个 case 中，可以出现多个结果选项（通过逗号分隔）；
- 与其它语言不同，Go 语言不需要用 break 来明确退出一个 case；
- 只有在 case 中明确添加 fallthrough 关键字，才会继续执行紧跟的下一个 case；
- 可以不设定 switch 之后的条件表达式，在这种情况下，整个 switch 结构与多个 if...else... 的逻辑作用等同。

## 1.3. 循环语句

- 和条件语句、分支语句一样，左花括号 { 必须与 for 处于同一行；
- 不支持 whie 和 do-while 结构的循环语句；
- 可以通过 for-range 结构对可迭代集合进行遍历；
- 支持基于条件判断进行循环迭代；
- 允许在循环条件中定义和初始化变量，且支持多重赋值；
- Go 语言的 for 循环同样支持 continue 和 break 来控制循环，但是它提供了一个更高级的 break，可以选择中断哪一个循环，如下例：

```go
JLoop: 
for j := 0; j < 5; j++ { 
    for i := 0; i < 10; i++ { 
        if i > 5 { 
            break JLoop
        }
        fmt.Println(i)
    } 
} 
```

## 1.4. 跳转语句

Go 语言的 break 和 contine 与其他语言的不同之处在于支持与标签结合跳转到指定的标签语句，从而改变这两个语句的默认跳转逻辑，标签语句通过标签 + :进行声明：

```go
arr := [][]int{{1,2,3},{4,5,6},{7,8,9}}
ITERATOR1:
for i := 0; i < 3; i++ {
    for j := 0; j < 3; j++ {
        num := arr[i][j]
        if j > 1 {
            break ITERATOR1
        }
        fmt.Println(num)
    }
}
```

Go 语言仍然支持 goto 关键字，goto 语句的语义非常简单，就是跳转到本函数内的某个标签

```go
arr := [][]int{{1,2,3},{4,5,6},{7,8,9}}
for i := 0; i < 3; i++ {
    for j := 0; j < 3; j++ {
        num := arr[i][j]
        if j > 1 {
            goto EXIT
        }
        fmt.Println(num)
    }
}   
EXIT:
fmt.Println("Exit.")
```
