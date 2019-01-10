[首页](https://printjs.github.io/blog) / [go语言](https://printjs.github.io/blog/docs/go) / [go指南](https://printjs.github.io/blog/docs/go/guide) / 导出名


# 变量和类型

## 变量

### 声明变量

`var`语句用于声明一个变量列表，跟函数的参数列表一样，类型在最后。

就像在这个例子中看到的一样，`var`语句可以出现在包或函数级别。

code
```go
package main

import "fmt"

var c, python, java bool

func main() {
    var i int
    fmt.Println(i, c, python, java)
}
```

### 变量初始化

变量声明可以包含初始值，每个变量对应一个。

如果初始化值已存在，则可以省略类型；变量会从初始值中获得类型。
```go
package main

import "fmt"

var i, j int = 1, 2

func main() {
    var c, python, java = true, false, "no!"
    fmt.Println(i, j, c, python, java)
}
```

### 短变量声明
在函数中，简洁赋值语句`:=`可在类型明确的地方代替`var`声明。

函数外的每个语句都必须以关键字开始（`var`,`func`等等），因此`:=`结构不能在函数外使用。
```go
package main

import "fmt"

func main() {
    var i, j int = 1, 2
    k := 3
    c, python, java := true, false, "no!"

    fmt.Println(i, j, k, c, python, java)
}
```

## 类型

### 基本类型
```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // uint8 的别名

rune // int32 的别名
    // 表示一个 Unicode 码点

float32 float64

complex64 complex128
```
本例展示了几种类型的变量。 同导入语句一样，变量声明也可以“分组”成一个语法块。

`int`, `uint` 和 `uintptr` 在 32 位系统上通常为 `32` 位宽，在 `64` 位系统上则为 `64` 位宽。 当你需要一个整数值时应使用 `int` 类型，除非你有特殊的理由使用固定大小或无符号的整数类型。

### 零值

没有明确初始值的变量声明会被赋予它们的*零值*。

零值是：
* 数值类型为 `0`，
* 布尔类型为 `false`，
* 字符串为 `""`（空字符串）。
```go
package main

import "fmt"

func main() {
    var i int
    var f float64
    var b bool
    var s string
    fmt.Printf("%v %v %v %q\n", i, f, b, s)
}
```

### 类型转换

表达式 T(v) 将值 v 转换为类型 T。

一些关于数值的转换：
```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```
或者，更加简单的形式：
```go
i := 42
f := float64(i)
u := uint(f)
```
与`C`不同的是，`Go`在不同类型的项之间赋值时需要显式转换。试着移除例子中 `float64`或`uint`的转换看看会发生什么。
```go
package main

import (
    "fmt"
    "math"
)

func main() {
    var x, y int = 3, 4
    var f float64 = math.Sqrt(float64(x*x + y*y))
    var z uint = uint(f)
    fmt.Println(x, y, z)
}
```

### 类型推导

在声明一个变量而不指定其类型时（即使用不带类型的`:=`语法或`var =`表达式语法），变量的类型由右值推导得出。

当右值声明了类型时，新变量的类型与其相同：
```go
var i int
j := i // j 也是一个 int
```
不过当右边包含未指明类型的数值常量时，新变量的类型就可能是`int`, `float64`或`complex128`了，这取决于常量的精度：
```go
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```
尝试修改示例代码中`v`的初始值，并观察它是如何影响类型的。
```go
package main

import "fmt"

func main() {
    v := 42 // 修改这里！
    fmt.Printf("v is of type %T\n", v)
}
```

### 常量
常量的声明与变量类似，只不过是使用 `const`关键字。

常量可以是*字符*、*字符串*、*布尔值*或*数值*。

常量不能用`:=`语法声明。
```go
package main

import "fmt"

const Pi = 3.14

func main() {
    const World = "世界"
    fmt.Println("Hello", World)
    fmt.Println("Happy", Pi, "Day")
    const Truth = true
    fmt.Println("Go rules?", Truth)
}
```

### 数值常量
数值常量是高精度的*值*。

一个未指定类型的常量由上下文来决定其类型。

再尝试一下输出`needInt(Big)`吧。

（`int`类型最大可以存储一个`64`位的整数，有时会更小。）

（`int`可以存放最大`64`位的整数，根据平台不同有时会更少。）
```go
package main

import "fmt"

const (
    // Create a huge number by shifting a 1 bit left 100 places.
    // In other words, the binary number that is 1 followed by 100 zeroes.
    Big = 1 << 100
    // Shift it right again 99 places, so we end up with 1<<1, or 2.
    Small = Big >> 99
)

func needInt(x int) int { return x*10 + 1 }
func needFloat(x float64) float64 {
    return x * 0.1
}

func main() {
    fmt.Println(needInt(Small))
    fmt.Println(needFloat(Small))
    fmt.Println(needFloat(Big))
}
```