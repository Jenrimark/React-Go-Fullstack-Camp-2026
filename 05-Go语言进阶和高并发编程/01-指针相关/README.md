# 指针相关

来源：
- https://campus.wps.cn/contentpreview/e51b8550-a7eb-4499-b24f-0c97ea809f88

# 指针

# 为什么需要指针？

让我们从一个简单的问题开始：

```
func modifyAge(age int) {
    age = age + 1
}

func main() {
    personAge := 25
    modifyAge(personAge)
    fmt.Println("年龄是:", personAge)  // 输出是多少？
}
```

你觉得这段代码会输出什么？

# 指针的基础概念

在 Go 语言中，函数参数默认是"值传递"，也就是说，函数会接收到参数的一个副本。当我们在函数内修改这个副本时，原始的值并不会改变。

让我们通过一个简单的例子来理解指针：

```
// 定义一个普通变量
name := "小明"

// 获取name的内存地址，用&符号
namePtr := &name

// 通过内存地址，获取对应的值，用*符号
value := *namePtr

fmt.Printf("name的值: %s\n", name)
fmt.Printf("name的内存地址: %p\n", &name)
fmt.Printf("namePtr存储的内存地址: %p\n", namePtr)
fmt.Printf("通过namePtr获取的值: %s\n", value)
```

& 符号是取地址符，用于获取变量的内存地址。

- 符号是解引用符，用于获取指针指向的值。

**结构体实例的指针变量会自动解引用**

注意：结构体实例的指针变量会自动解引用，因此不需要使用 \* 符号。例如：

```
package main

import "fmt"

// 定义一个结构体
type Person struct {
	Name string
	Age  int
}

// 为Person结构体定义一个方法
func (p Person) PrintInfo() {
	fmt.Printf("Name: %s, Age: %d\n", p.Name, p.Age)
}

func main() {
	// 创建一个Person结构体指针
	personPtr := &Person{
		Name: "Alice",
		Age:  30,
	}
	(*personPtr).PrintInfo()
    // 使用指针调用方法，这里会自动解引用
	personPtr.PrintInfo()
}
```

在上述代码中，PrintInfo 方法是为 Person 结构体（值接收者）定义的。当我们使用 personPtr 这个指向 Person 结构体的指针来调用 PrintInfo 方法时，Go 语言会自动将指针解引用，就好像我们是通过结构体值来调用方法一样。

## 理解指针：生活中的类比

想象你住在一个大型公寓楼里：

- 你的房间号（比如 301）就像是内存地址
- 房间里的物品就像是变量的值
- 如果你要送快递给邻居，你需要知道他的房间号

在编程中：

- 内存地址就像房间号
- 变量的值就像房间里的物品
- 指针就像是记录着房间号的便利贴

📝 **练习：创建和使用指针**

尝试运行下面的代码，观察输出结果：

```
package main

import "fmt"

func main() {
    // 1. 创建一个字符串变量
    message := "你好，Go!"

    // 2. 创建一个指向message的指针
    // 请在这里补充代码

    // 3. 通过指针修改message的值
    // 请在这里补充代码

    // 4. 打印结果
    fmt.Println("修改后的message:", message)
}
```

# 指针的创建方式

在 Go 语言中，创建指针有三种常用方法：

**1. 从现有变量获取指针**

先定义对应的变量，再通过变量取得内存地址，创建指针。

```
age := 25
agePtr := &age
```

**2. 使用 new 函数**

先使用 new 函数创建指针，分配好内存后，再给指针指向的内存地址写入对应的值。

```
scorePtr := new(int)
*scorePtr = 100
```

**3. 声明后再赋值**

先声明一个指针变量，再从其他变量取得内存地址赋值给它。

```
var namePtr *string
name := "小红"
namePtr = &name
```

📝 **练习：三种方式创建指针**

```
package main

import "fmt"

func main() {
    // 1. 使用第一种方式创建一个int类型的指针

    // 2. 使用new函数创建一个float64类型的指针

    // 3. 使用声明后赋值的方式创建一个string类型的指针

    // 打印所有结果
}
```

# 指针的实际应用

## 1. 使用指针优化函数参数传递

让我们改进开始时的那个例子：

```
func modifyAge(age *int) {
    *age = *age + 1
}

func main() {
    personAge := 25
    modifyAge(&personAge)
    fmt.Println("年龄是:", personAge)  // 现在会输出26
}
```

## 2. 实现 swap 函数

```
func swap(a, b *int) {
    temp := *a
    *a = *b
    *b = temp
}

func main() {
    x, y := 1, 2
    fmt.Printf("交换前：x=%d, y=%d\n", x, y)
    swap(&x, &y)
    fmt.Printf("交换后：x=%d, y=%d\n", x, y)
}
```

# 指针的类型

我们知道字符串的类型是 `string`，整型是 `int`，那么指针如何表示呢？写段代码试验一下就知道了：

```
package main

import "fmt"

func main() {
    astr := "hello"
    aint := 1
    abool := false
    arune := 'a'
    afloat := 1.2

    fmt.Printf("astr 指针类型是：%T\n", &astr)
    fmt.Printf("aint 指针类型是：%T\n", &aint)
    fmt.Printf("abool 指针类型是：%T\n", &abool)
    fmt.Printf("arune 指针类型是：%T\n", &arune)
    fmt.Printf("afloat 指针类型是：%T\n", &afloat)
}
```

输出如下，可以发现用 `*` + 所指向变量值的数据类型，就是对应的指针类型。

```
astr 指针类型是：*string
aint 指针类型是：*int
abool 指针类型是：*bool
arune 指针类型是：*int32
afloat 指针类型是：*float64
```

所以若我们定义一个只接收指针类型的参数的函数，可以这么写：

```
func mytest(ptr *int)  {
    fmt.Println(*ptr)
}
```

# 指针的零值

当指针声明后，没有进行初始化，其零值是 `nil`。

```
func main() {
    a := 25
    var b *int  // 声明一个指针

    if b == nil {
        fmt.Println(b)
        b = &a  // 初始化：将a的内存地址给b
        fmt.Println(b)
    }
}
```

输出如下：

```
<nil>
0xc0000100a0
```

# 指针与切片

切片与指针一样，都是引用类型。

如果我们想通过一个函数改变一个数组的值，有两种方法：

- 将这个数组的切片做为参数传给函数
- 将这个数组的指针做为参数传给函数

尽管二者都可以实现我们的目的，但是按照 Go 语言的使用习惯，建议使用第一种方法，因为第一种方法，写出来的代码会更加简洁，易读。具体你可以参考下面两种方法的代码实现：

## 使用切片

```
func modify(sls []int) {
    sls[0] = 90
}

func main() {
    a := [3]int{89, 90, 91}
    modify(a[:])
    fmt.Println(a)
}
```

# 使用指针

```
func modify(arr *[3]int) {
    (*arr)[0] = 90
}

func main() {
    a := [3]int{89, 90, 91}
    modify(&a)
    fmt.Println(a)
}
```

# make 和 new 的区别

在 Go 语言中，`make` 和 `new` 都是用于内存分配的内置函数，但它们有着不同的用途和行为：

### 1. 适用类型

- **`make`**：仅适用于创建 `slice`、`map` 和 `channel` 这三种引用类型。
- **`new`**：用于为任何类型分配零值内存空间，返回指向该类型零值的指针。它适用于所有类型，包括结构体、基本类型（如 `int`、`float` 等）以及指针类型等。

### 2. 返回值

- **`make`**：返回的是类型本身，而不是指向类型的指针。例如，`make([]int, 5)` 返回的是一个 `int` 类型的切片 `[]int`，`make(map[string]int)` 返回一个 `map[string]int` 类型的 `map`，`make(chan int)` 返回一个 `chan int` 类型的通道。
- **`new`**：返回一个指向已分配内存的指针。例如，`new(int)` 返回一个指向 `int` 类型零值（即 `0`）的指针 `*int`，`new(struct { a int; b string })` 返回一个指向该结构体零值（`a` 为 `0`，`b` 为空字符串）的指针 `*struct { a int; b string }`。

### 3. 内存初始化

- **`make`**：不仅分配内存，还会对创建的 `slice`、`map` 或 `channel` 进行初始化。
  - 对于 `slice`，会根据指定的长度初始化元素，长度范围内的元素为其类型的零值。例如，`s := make([]int, 3)` 创建的切片 `s` 长度为 3，三个元素都初始化为 `0`。
  - 对于 `map`，会初始化一个空的 `map`，可以立即用于插入键值对。
  - 对于 `channel`，会初始化一个指定类型的通道，根据是否带缓冲进行相应初始化。例如，`ch := make(chan int)` 创建一个无缓冲通道，`ch := make(chan int, 5)` 创建一个带 5 个缓冲的通道。
- **`new`**：只是分配内存并将内存清零，即填充为类型的零值。例如，`p := new(int)`，此时 `*p` 的值为 `0`，但如果是自定义结构体 `type MyStruct struct { a int; b string }`，`p := new(MyStruct)`，则 `p.a` 为 `0`，`p.b` 为空字符串，并没有对结构体内部进行额外的初始化操作。

### 4. 示例代码

```
package main

import (
    "fmt"
)

func main() {
    // make 示例
    // 创建并初始化一个切片
    slice := make([]int, 3)
    fmt.Printf("Slice: %v\n", slice)

    // 创建并初始化一个 map
    m := make(map[string]int)
    m["key"] = 1
    fmt.Printf("Map: %v\n", m)

    // 创建并初始化一个通道
    ch := make(chan int)
    fmt.Printf("Channel: %v\n", ch)

    // new 示例
    numPtr := new(int)
    fmt.Printf("Pointer to int: %v, Value: %d\n", numPtr, *numPtr)

    type MyStruct struct {
        a int
        b string
    }
    structPtr := new(MyStruct)
    fmt.Printf("Pointer to struct: %v, a: %d, b: %s\n", structPtr, structPtr.a, structPtr.b)
}
```

在上述代码中，可以清楚看到 `make` 和 `new` 在适用类型、返回值以及内存初始化方面的不同表现。`make` 专注于创建并初始化特定的引用类型，而 `new` 用于为各种类型分配零值内存并返回指针。

# 其他教程

<https://www.topgoer.com/go%E5%9F%BA%E7%A1%80/%E6%8C%87%E9%92%88.html>

<https://go.sixue.work/golang/c01/c01_08.html>

[Go语言基础：指针](https://campus.wps.cn/contentpreview/9321ea1c-fdcd-4076-9d2d-86458a63b603)
