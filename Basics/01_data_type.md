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
  - [1.4. 类型转换](#14-类型转换)
    - [1.4.1. 整型与浮点型之间的转化](#141-整型与浮点型之间的转化)
    - [1.4.2. 字符串和其他基本类型之间的转化](#142-字符串和其他基本类型之间的转化)
    - [1.4.3. `strconv`包](#143-strconv包)
  - [1.5. 数组](#15-数组)
    - [1.5.1. 访问和遍历](#151-访问和遍历)
    - [1.5.2. 数组类型的不足](#152-数组类型的不足)
  - [1.6. 切片](#16-切片)
    - [1.6.1. 创建切片](#161-创建切片)
      - [1.6.1.1. 基于数组](#1611-基于数组)
      - [1.6.1.2. 基于切片](#1612-基于切片)
      - [1.6.1.3. 直接创建](#1613-直接创建)
    - [1.6.2. 遍历切片](#162-遍历切片)
    - [1.6.3. 动态增加元素](#163-动态增加元素)
    - [1.6.4. 自动扩容](#164-自动扩容)
    - [1.6.5. 内存复制](#165-内存复制)
    - [1.6.6. 动态删除元素](#166-动态删除元素)
    - [1.6.7. 数据共享问题](#167-数据共享问题)
  - [1.7. 字典](#17-字典)
    - [1.7.1. 声明&初始化](#171-声明初始化)
    - [1.7.2. 使用方式](#172-使用方式)
  - [1.8. 指针&unsafe.Pointer](#18-指针unsafepointer)
    - [1.8.1. unsafe.Pointer](#181-unsafepointer)
      - [1.8.1.1. 指针类型转换](#1811-指针类型转换)
      - [1.8.1.2. 指针运算实现](#1812-指针运算实现)

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

![output](https://raw.githubusercontent.com/TDAkory/ImageResources/master/img/20211126104313.png)

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

## 1.4. 类型转换

### 1.4.1. 整型与浮点型之间的转化

```go
// 整型和浮点型之间的转化，浮点型转化为整型时，小数位被丢弃：
v1 := 99.99
v2 := int(v1)  // v2 = 99

// 将整型转化为浮点型时，比较简单，直接调用对应的函数即可：
v1 := 99
v2 := float64(v2). // v2 = 99
```

### 1.4.2. 字符串和其他基本类型之间的转化

```go
// Unicode 字符集转化为对应的 UTF-8 编码的字符串, Unicode 兼容 ASCII 字符集，所以 65 被转化为 A。
v1 := 65
v2 := string(v1)  // v2 = A
v3 := 30028
v4 := string(v3)  // v4 = 界

// byte 是 uint8 的别名，rune 是 int32 的别名
v1 := []byte{'h', 'e', 'l', 'l', 'o'}
v2 := string(v1)  // v2 = hello
v3 := []rune{0x5b66, 0x9662, 0x541b}
v4 := string(v3)  // v4 = 学院君
```

### 1.4.3. `strconv`包

Go 语言默认不支持将字符串类型强制转化为数值类型

```go
v1 := "100"
v2, _ := strconv.Atoi(v1)  // 将字符串转化为整型，v2 = 100

v3 := 100
v4 := strconv.Itoa(v3)   // 将整型转化为字符串, v4 = "100"

v5 := "true"
v6, _ := strconv.ParseBool(v5)  // 将字符串转化为布尔型
v5 = strconv.FormatBool(v6)  // 将布尔值转化为字符串

v7 := "100"
v8, _ := strconv.ParseInt(v7, 10, 64)   // 将字符串转化为整型，第二个参数表示进制，第三个参数表示最大位数
v7 = strconv.FormatInt(v8, 10)   // 将整型转化为字符串，第二个参数表示进制

v9, _ := strconv.ParseUint(v7, 10, 64)   // 将字符串转化为无符号整型，参数含义同 ParseInt
v7 = strconv.FormatUint(v9, 10)  // 将字符串转化为无符号整型，参数含义同 FormatInt
v10 := "99.99"

v11, _ := strconv.ParseFloat(v10, 64)   // 将字符串转化为浮点型，第二个参数表示精度
v10 = strconv.FormatFloat(v11, 'E', -1, 64)

q := strconv.Quote("Hello, 世界")    // 为字符串加引号
q = strconv.QuoteToASCII("Hello, 世界")  // 将字符串转化为 ASCII 编码
```

## 1.5. 数组

```go
var a [8]byte // 长度为8的数组，每个元素为一个字节
var b [3][3]int // 二维数组（9宫格）
var c [3][3][3]float64 // 三维数组（立体的9宫格）
var d = [3]int{1, 2, 3}  // 声明时初始化
var e = new([3]string)   // 通过 new 初始化

a := [...]int{1, 2, 3}  // 语法糖省略长度声明
```

数组长度在声明后就不可更改，在声明时可以指定数组长度为一个常量或者一个常量表达式。数组的长度是该数组类型的一个内置常量，可以用 Go 语言的内置函数 len() 来获取。

### 1.5.1. 访问和遍历

```go
arr := [5]int{1,2,3,4,5}
a1, a2 := arr[0], arr[len(arr) - 1]     // a1 的值是 1，a2 的值是 5

for i := 0; i < len(arr); i++ {
    fmt.Println("Element", i, "of arr is", arr[i])
}

// 返回下标和索引
for i, v := range arr { 
    fmt.Println("Element", i, "of arr is", v) 
}
```

### 1.5.2. 数组类型的不足

由于数组类型变量一旦声明后长度就固定了，这意味着我们将不能动态添加元素到数组

数组是值类型，这意味着作为参数传递到函数时，传递的是数组的值拷贝，也就是说，会先将数组拷贝给形参，然后在函数体中引用的是形参而不是原来的数组，当我们在函数中对数组元素进行修改时，并不会影响原来的数组，这种机制带来的另一个负面影响是当数组很大时，值拷贝会降低程序性能。

## 1.6. 切片

切片的类型字面量中只有元素的类型

```go
var slice []string = []string{"a", "b", "c"}
```

### 1.6.1. 创建切片

#### 1.6.1.1. 基于数组

```go
// 先定义一个数组
months := [...]string{"January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"}
// 基于数组创建切片
q2 := months[3:6]       // 第二季度
summer := months[5:8]   // 夏季
fmt.Println(q2)         // [April May June]
fmt.Println(summer)     // [June July August]

all := months[:]        // 基于 months 的所有元素创建切片
```

切片底层引用了一个数组，由三个部分构成 —— 指针、长度和容量，指针指向数组起始下标，长度对应切片中元素的个数，容量则是切片起始位置到底层数组结尾的位置。

#### 1.6.1.2. 基于切片

```go
firsthalf := months[:6]
q1 := firsthalf[:3] // 基于 firsthalf 的前 3 个元素构建新切片
```

#### 1.6.1.3. 直接创建

```go
mySlice1 := make([]int, 5)          // 初始长度为 5 的整型切片

mySlice2 := make([]int, 5, 10)      // 初始长度为 5、容量为 10 的整型切片

mySlice3 := []int{1, 2, 3, 4, 5}    // 创建并初始化包含 5 个元素的数组切片（长度和容量均为5）
```

### 1.6.2. 遍历切片

切片可以看作数组指针，因此遍历切片和遍历数组类似

### 1.6.3. 动态增加元素

一个切片的容量初始值根据创建方式的不同而不同：

- 对于基于数组和切片创建的切片而言，默认容量是从切片起始索引到对应底层数组的结尾索引；
- 对于通过内置 make 函数创建的切片而言，在没有指定容量参数的情况下，默认容量和切片长度一致。

```go
var oldSlice = make([]int, 5, 10)
fmt.Println("len(oldSlice):", len(oldSlice))    // len(oldSlice): 5
fmt.Println("cap(oldSlice):", cap(oldSlice))    // cap(oldSlice): 10

newSlice := append(oldSlice, 1, 2, 3)

appendSlice := []int{1, 2, 3, 4, 5}
newSlice := append(oldSlice, appendSlice...)  // 将一个切片追加到另一个切片的末尾, 注意末尾的 ... 不能省略
```

### 1.6.4. 自动扩容

如果追加的元素超过slice的容量，底层会自动扩容。append() 函数并不会改变原来的切片，而是会生成一个容量更大的切片，然后把原有的元素和新元素一并拷贝到新切片中。

默认情况下，扩容后新切片的容量将会是原切片容量的 2 倍。但是，当原切片的长度大于或等于 1024 时，Go 语言将会以原容量的 1.25 倍作为新容量的基准。

### 1.6.5. 内存复制

切片类型还支持 Go 语言的另一个内置函数 copy()，用于将元素从一个切片复制到另一个切片。如果两个切片不一样大，就会按其中较小的那个切片的元素个数进行复制。

```go
slice1 := []int{1, 2, 3, 4, 5} 
slice2 := []int{5, 4, 3}
// 复制 slice1 到 slice 2
copy(slice2, slice1) // 只会复制 slice1 的前3个元素到 slice2 中
// slice2 结果: [1, 2, 3]
// 复制 slice2 到 slice 1
copy(slice1, slice2) // 只会复制 slice2 的 3 个元素到 slice1 的前 3 个位置
// slice1 结果：[5, 4, 3, 4, 5]
```

### 1.6.6. 动态删除元素

```go
slice3 := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
slice3 = slice3[:len(slice3) - 5]  // 删除 slice3 尾部 5 个元素
slice3 = slice3[5:]  // 删除 slice3 头部 5 个元素

// 通过append接口和copy接口实现删除
slice3 := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
    
slice4 := append(slice3[:0], slice3[3:]...)  // 删除开头三个元素
slice5 := append(slice3[:1], slice3[4:]...)  // 删除中间三个元素
slice6 := append(slice3[:0], slice3[:7]...)  // 删除最后三个元素
slice7 := slice3[:copy(slice3, slice3[3:])]  // 删除开头前三个元素, copy 之所以可以用于删除元素，是因为其返回值是拷贝成功的元素个数，我们可以根据这个值完成新切片的设置从而达到「删除」元素的效果
```

### 1.6.7. 数据共享问题

```go
type slice struct {
  array unsafe.Pointer
  len   int
  cap   int
}
```

在结构体中使用指针存在不同实例的数据共享问题：

```go
slice1 := []int{1, 2, 3, 4, 5}
slice2 := slice1[1:3]
slice2[1] = 6
fmt.Println("slice1:", slice1)
fmt.Println("slice2:", slice2)

#### Answer
slice1: [1 2 6 4 5]
slice2: [2 6]
```

解决方式，可以如下

```go
slice1 := make([]int, 4)
slice2 := slice1[1:3]
slice1 = append(slice1, 0)  // append会重新分配内存
slice1[1] = 2
slice2[1] = 6
fmt.Println("slice1:", slice1)
fmt.Println("slice2:", slice2)

#### Answer
slice1: [0 2 0 0 0]
slice2: [0 6]
```

## 1.7. 字典

Go字典是个无序集合

```go
var testMap map[string]int
testMap = map[string]int {
  "one": 1,
  "two": 2,
  "three": 3,
}

k := "two"
v, ok := testMap[k]
if ok {
  fmt.Printf("The element of key %q: %d\n", k, v)
} else {
  fmt.Printf("Not found!")
}
```

### 1.7.1. 声明&初始化

```go
// 声明
var testMap map[string]int

// 初始化
testMap := map[string]int{  // 初始化之后不能再添加键值对，否则会panic
  "one": 1, 
  "two": 2,
  "three": 3,
}

var testMap = make(map[string]int)
testMap["one"] = 1
testMap["two"] = 2
testMap["three"] = 3

testMap = make(map[string]int, 100) // 创建时指定该字典的初始存储容量（超出会自动扩容）
```

### 1.7.2. 使用方式

```go
testMap["four"] = 4 // 字典初始化之后才能进行赋值操作

value, ok := testMap["one"]   // 查找，会返回两个值
if ok { // 找到了
  // 处理找到的value 
}

delete(testMap, "forr")   // 删除元素，若键不存在或者字典未初始化，则无事发生

// 遍历字典
for key, value := range testMap {
  fmt.Println(key, value)
}
// 利用匿名变量进行遍历
for _, value := range testMap {
    fmt.Println(value)
}
// 只获取字典的键
for key := range testMap {
    fmt.Println(key)
}

// 键值对调
invMap := make(map[int] string, 3)
for k, v := range testMap {
    invMap[v] = k
}

// 排序，分别为字典的键和值创建切片，然后对切片进行排序来实现
keys := make([]string, 0)
for k, _ := range testMap {
    keys = append(keys, k)
}
sort.Strings(keys)  // 对键进行排序
fmt.Println("Sorted map by key:")
for _, k := range keys {
    fmt.Println(k, testMap[k])
}

values := make([]int, 0)
for _, v := range testMap {
    values = append(values, v)
}
sort.Ints(values)   // 对值进行排序
fmt.Println("Sorted map by value:")
for _, v := range values  {
    fmt.Println(invMap[v], v)
}
```

## 1.8. 指针&unsafe.Pointer

```go
a := 100
var ptr *int  // 声明指针类型
ptr = &a      // 初始化指针类型值为变量 a 
fmt.Println(ptr)    // 0xc0000a2000
fmt.Println(*ptr)   // 100

fmt.Printf("%p\n", ptr)   // 通过 %p 来标识指针类型
fmt.Printf("%d\n", *ptr)
```

### 1.8.1. unsafe.Pointer

`unsafe.Pointer` 是特别定义的一种指针类型，它可以包含任意类型变量的地址（类似 C 语言中的 `void` 类型指针）。Go 官方文档对这个类型有如下四个描述：

- 任何类型的指针都可以被转化为 `unsafe.Pointer`；
- `unsafe.Pointer` 可以被转化为任何类型的指针；
- `uintptr` 可以被转化为 `unsafe.Pointer`；
- `unsafe.Pointer` 可以被转化为 `uintptr`;

#### 1.8.1.1. 指针类型转换

```go
i := 10
var p *int = &i

var fp *float32 = (*float32)(unsafe.Pointer(p))
*fp = *fp * 10
fmt.Println(i)  // 100
```

#### 1.8.1.2. 指针运算实现

uintptr 是 Go 内置的可用于存储指针的整型，而整型是可以进行数学运算的！因此，将 unsafe.Pointer 转化为 uintptr 类型后，就可以让本不具备运算能力的指针具备了指针运算能力：

```go
arr := [3]int{1, 2, 3}
ap := &arr
sp := (*int)(unsafe.Pointer(uintptr(unsafe.Pointer(ap)) + unsafe.Sizeof(arr[0])))
*sp += 3
fmt.Println(arr)  // [1 5 3]
```