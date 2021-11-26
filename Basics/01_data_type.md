# 1. 数据类型

- [1. 数据类型](#1-数据类型)
  - [1.1. 变量](#11-变量)
    - [1.1.1. 声明和命名规则](#111-声明和命名规则)
    - [1.1.2. 初始化](#112-初始化)
    - [1.1.3. 赋值和多重赋值](#113-赋值和多重赋值)
    - [1.1.4. 匿名变量](#114-匿名变量)
    - [1.1.5. 作用域](#115-作用域)
  - [1.2. 常量](#12-常量)
    - [1.2.1. 定义](#121-定义)
    - [1.2.2. 预定义常量](#122-预定义常量)
    - [1.2.3. 枚举](#123-枚举)
  - [1.3. 支持的数据类型](#13-支持的数据类型)
    - [1.3.1. 注意点](#131-注意点)
    - [1.3.2. 整形](#132-整形)
    - [1.3.3. 运算符](#133-运算符)
    - [1.3.4. 浮点数](#134-浮点数)
    - [1.3.5. 复数类型](#135-复数类型)
    - [1.3.6. 字符串](#136-字符串)
      - [1.3.6.1. 转义字符](#1361-转义字符)
      - [1.3.6.2. 字符串操作](#1362-字符串操作)
  - [类型转换](#类型转换)

## 1.1. 变量

### 1.1.1. 声明和命名规则

```go
var v1 int  // 声明int类型变量

var (       // 放在一起声明
    v1 int
    v2 int
)

var v1 int              // 整型
var v2 string           // 字符串
var v3 bool             // 布尔型
var v4 [10]int          // 数组，数组元素类型为整型
var v5 struct {         // 结构体，成员变量 f 的类型为64位浮点型
    f float64
} 
var v6 *int             // 指针，指向整型
var v7 map[string]int   // map（字典），key为字符串类型，value为整型
var v8 func(a int) int  // 函数，参数类型为整型，返回值类型为整型
```

变量在声明之后，系统会自动将变量值初始化为对应类型的零值，比如上述 v1 的值为 0，v2 的值空字符串，v3 的值为 false，依次类推，我们打印上述变量的值，可以看到如下输出：

![output](https://raw.githubusercontent.com/TDAkory/ImageResources/main/img/20211126104313.png)

### 1.1.2. 初始化

```go
var v1 int = 10   // 方式一，常规的初始化操作
var v2 = 10       // 方式二，此时变量类型会被编译器自动推导出来
v3 := 10          // 方式三，可以省略 var，编译器可以自动推导出v3的类型
```

### 1.1.3. 赋值和多重赋值

```go
var v10 int 
v10 = 123

i, j = j, i
```

### 1.1.4. 匿名变量

```go
func GetName() (userName, nickName string) {
    return "momfu", "haha"
}

_, nickName := GetName()    // 若只想获得 nickName，则用`_`替代其他变量
```

### 1.1.5. 作用域

如果一个变量在函数体外声明，则被认为是全局变量，可以在整个包甚至外部包（变量名以大写字母开头）使用，不管你声明在哪个源文件里或在哪个源文件里调用该变量。在函数体内声明的变量称之为局部变量，它们的作用域只在函数体内，函数的参数和返回值变量也是局部变量。

尽管变量的标识符必须是唯一的，但你可以在某个代码块的内层代码块中使用相同名称的变量，此时外部的同名变量将会暂时隐藏（结束内部代码块的执行后隐藏的外部同名变量又会出现，而内部同名变量则被释放），你任何的操作都只会影响内部代码块的局部变量。

## 1.2. 常量

### 1.2.1. 定义

```go
const Pi float64 = 3.14159265358979323846 
const zero = 0.0 // 无类型浮点常量 
const (          // 通过一个 const 关键字定义多个常量，和 var 类似
    size int64 = 1024
    eof = -1  // 无类型整型常量 
) 
const u, v float32 = 0, 3  // u = 0.0, v = 3.0，常量的多重赋值 
const a, b, c = 3, 4, "foo" // a = 3, b = 4, c = "foo", 无类型整型和字符串常量
```

### 1.2.2. 预定义常量

`true` `false` `iota`

iota 比较特殊，可以被认为是一个可被编译器修改的常量，在每一个 const 关键字出现时被重置为 0，然后在下一个 const 出现之前，每出现一次 iota，其所代表的数字会自动增 1。

```go
package main
const (    // iota 被重置为 0
    c0 = iota   // c0 = 0
    c1 = iota   // c1 = 1
    c2 = iota   // c2 = 2
)
const (
    u = iota * 2;  // u = 0
    v = iota * 2;  // v = 2
    w = iota * 2;  // w = 4
)
const x = iota;  // x = 0
const y = iota;  // y = 0
```

如果两个 const 的赋值语句的表达式是一样的，那么还可以省略后一个赋值表达式。因此，上面的前两个 const 语句可简写为：

```go
const ( 
    c0 = iota 
    c1 
    c2 
)
const ( 
    u = iota * 2 
    v 
    w 
)
```

### 1.2.3. 枚举

Go 语言并不支持其他语言用于表示枚举的 enum 关键字，而是通过在 const 后跟一对圆括号定义一组常量的方式来实现枚举。

```go
const (
    Sunday = iota 
    Monday 
    Tuesday 
    Wednesday 
    Thursday 
    Friday 
    Saturday 
    numberOfDays
)
```

## 1.3. 支持的数据类型

Go 语言内置对以下这些基本数据类型的支持：

- 布尔类型：bool
- 整型：int8、byte、int16、int、uint、uintptr 等
- 浮点类型：float32、float64
- 复数类型：complex64、complex128
- 字符串：string
- 字符类型：rune
- 错误类型：error

此外，Go 语言还支持以下这些复合类型：

- 指针（pointer）
- 数组（array）
- 切片（slice）
- 字典（map）
- 通道（chan）
- 结构体（struct）
- 接口（interface）

### 1.3.1. 注意点

- 由于强类型的缘故，Go 语言在进行布尔值真假判断时，对值的类型有严格限制。不同类型的值不能使用 == 或 != 运算符进行比较，在编译期就会报错
  
  ```go
  b := (false == 0)
  ```

  ```shell
  cannot convert 0 (type untyped number) to type bool
  invalid operation: false == 0 (mismatched types bool and int)
  ```

### 1.3.2. 整形

`int8` `uint8` `int16` `uint16` `int32` `uint32` `int64` `uint64` `int` `uint` `uintptr`

此外 int 和 int32 在 Go 语言里被认为是两种不同的类型（同理，int 和 int64 也是不同的类型）

```go
intValue1 = int8(intValue2)
```

### 1.3.3. 运算符

略

### 1.3.4. 浮点数

Go 语言中的浮点数采用IEEE-754 标准的表达方式，定义了两个类型：`float32` 和 `float64`

对于浮点类型需要被自动推导的变量，其类型将被自动设置为 float64，而不管赋值给它的数字是否是用 32 位长度表示的。

判断相等需要特殊处理:

```go
p := 0.00001
// 判断 floatValue1 与 floatValue2 是否相等
if math.Dim(float64(floatValue1), floatValue2) < p {
    fmt.Println("floatValue1 和 floatValue2 相等")
} 
```

### 1.3.5. 复数类型

```go

var complexValue1 complex64        
complexValue1 = 1.10 + 10i          // 由两个 float32 实数构成的复数类型
complexValue2 := 1.10 + 10i         // 和浮点型一样，默认自动推导的实数类型是 float64，所以 complexValue2 是 complex128 类型
complexValue3 := complex(1.10, 10)  // 与 complexValue2 等价
```

### 1.3.6. 字符串

在 Go 语言中，字符串是一种不可变值类型，一旦初始化之后，它的内容不能被修改

```go
var str string         // 声明字符串变量
str = "Hello World"    // 变量初始化
str2 := "你好，学院君"   // 也可以同时进行声明和初始化

fmt.Printf("The length of \"%s\" is %d \n", str, len(str)) 
fmt.Printf("The first character of \"%s\" is %c.\n", str, ch)

// 多行 包含双引号
results := `Search results for "Golang":
- Go
- Golang
Golang Programming
`
fmt.Printf("%s", results)
```

#### 1.3.6.1. 转义字符

- \n ：换行符
- \r ：回车符
- \t ：tab 键
- \u 或 \U ：Unicode 字符
- \\ ：反斜杠自身

#### 1.3.6.2. 字符串操作

```go
// 字符串连接
str = str + ", 学院君"
str += ", 学院君"  // 上述语句也可以简写为这样，效果完全一样

// 切片
str := "hello, world"
str1 := str[:5]  // 获取索引5（不含）之前的子串
str2 := str[7:]  // 获取索引7（含）之后的子串
str3 := str[0:5]  // 获取从索引0（含）到索引5（不含）之间的子串
fmt.Println("str1:", str1)
fmt.Println("str2:", str2)
fmt.Println("str3:", str3)

// 以字节数组的方式遍历
str := "Hello, 世界" 
n := len(str) 
for i := 0; i < n; i++ {
    ch := str[i]    // 依据下标取字符串中的字符，ch 类型为 byte
    fmt.Println(i, ch) 
}

// 以 Unicode 字符遍历
str := "Hello, 世界" 
for i, ch := range str { 
    fmt.Println(i, ch)    // ch 的类型为 rune 
}

// Unicode 编码转化为可打印字符
str := "Hello, 世界" 
for i, ch := range str { 
    fmt.Println(i, string(ch))
}
```

Go 语言对字符串中的单个字符进行了单独的类型支持，在 Go 语言中支持两种字符类型：

- 一种是 byte，代表 UTF-8 编码中单个字节的值（它也是 uint8 类型的别名，两者是等价的，因为正好占据 1 个字节的内存空间）；
- 另一种是 rune，代表单个 Unicode 字符（它也是 int32 类型的别名，因为正好占据 4 个字节的内存空间。关于 rune 相关的操作，可查阅 Go 标准库的 unicode 包）。

## 类型转换