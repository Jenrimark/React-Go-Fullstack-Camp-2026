# 函数

来源：
- https://campus.wps.cn/contentpreview/1215b8e4-6439-402d-8f48-3199456026e9

# 函数

## 1. 函数的定义

函数是Go程序的基本执行单元，通过`func`关键字定义。Go语言的函数具有声明简洁、支持多返回值等特性，是实现代码复用和逻辑封装的核心方式。

### 基本语法结构

```
func 函数名(参数列表) (返回值类型列表) {
    // 函数体
    return 返回值列表
}
```

### 命名规范

- 函数名采用**驼峰式命名**（CamelCase）
- 首字母大写：函数可被其他包访问（公开函数）
- 首字母小写：仅当前包可见（私有函数）

### 基础示例

```
// 加法函数（公开函数）
func Add(a int, b int) int {
    return a + b
}

// 计算两数之差（私有函数）
func subtract(a, b int) int { // 参数列表中相同类型可合并声明
    return a - b
}
```

## 2. 参数

Go函数参数分为值传递、引用传递和可变参数三种类型，参数传递机制直接影响函数对外部变量的修改能力。

### 值传递

函数接收参数的副本，修改参数不会影响原始值：

```
func increment(x int) {
    x++
    fmt.Println("函数内：", x) // 输出：函数内： 11
}

func main() {
    a := 10
    increment(a)
    fmt.Println("函数外：", a) // 输出：函数外： 10（原始值未改变）
}
```

### 引用传递（指针参数）

通过指针传递参数地址，函数内修改会影响原始值：

```
func incrementPtr(x *int) {
    *x++ // 修改指针指向的原始值
}

func main() {
    a := 10
    incrementPtr(&a)
    fmt.Println(a) // 输出：11（原始值被修改）
}
```

### 可变参数

使用`...type`声明可接收任意数量的同类型参数，在函数内部表现为切片：

```
// 计算任意数量整数的和
func sum(nums ...int) int {
    total := 0
    for _, num := range nums {
        total += num
    }
    return total
}

func main() {
    fmt.Println(sum(1, 2, 3))      // 输出：6
    fmt.Println(sum(10, 20, 30, 40)) // 输出：100
    
    // 传递切片时需解包
    numbers := []int{1, 2, 3, 4}
    fmt.Println(sum(numbers...))   // 输出：10
}
```

## 3. 返回值

Go语言支持单返回值、多返回值和命名返回值，其中多返回值特性常用于返回结果和错误信息。

### 单返回值

最基础的返回形式，直接指定返回值类型：

```
func square(x int) int {
    return x * x
}
```

### 多返回值

通过逗号分隔多个返回值类型，常用于返回结果和错误：

```
// 除法运算，返回商和余数
func divide(a, b int) (int, int) {
    quotient := a / b
    remainder := a % b
    return quotient, remainder
}

func main() {
    q, r := divide(10, 3)
    fmt.Printf("商：%d, 余数：%d\n", q, r) // 输出：商：3, 余数：1
}
```

### 命名返回值

在函数声明时为返回值命名，可直接在函数体内赋值：

```
// 命名返回值自动初始化零值，可直接return
func calculate(a, b int) (sum, product int) {
    sum = a + b        // 直接赋值给命名返回值
    product = a * b
    return             // 无需显式指定返回值
}
```

## 4. 匿名函数

匿名函数是没有函数名的函数，可直接赋值给变量或作为参数传递，常用于简化代码和实现回调。

### 基本用法

```
func main() {
    // 赋值给变量
    add := func(a, b int) int {
        return a + b
    }
    fmt.Println(add(2, 3)) // 输出：5

    // 立即执行函数（IIFE）
    result := func(x, y int) int {
        return x * y
    }(3, 4)
    fmt.Println(result) // 输出：12
}
```

### 作为回调函数

```
// 排序回调示例
func sortNumbers(nums []int, compare func(int, int) bool) {
    for i := 0; i < len(nums); i++ {
        for j := i + 1; j < len(nums); j++ {
            if compare(nums[i], nums[j]) {
                nums[i], nums[j] = nums[j], nums[i]
            }
        }
    }
}

func main() {
    numbers := []int{3, 1, 4, 1, 5}
    
    // 匿名函数作为排序规则
    sortNumbers(numbers, func(a, b int) bool {
        return a > b // 降序排序
    })
    fmt.Println(numbers) // 输出：[5 4 3 1 1]
}
```

## 5. 闭包

闭包是函数及其引用环境的组合，能够捕获并访问外部作用域的变量，即使外部函数已执行完毕。

### 基本概念

```
func outer() func() int {
    count := 0          // 被闭包捕获的外部变量
    return func() int {
        count++         // 访问外部变量
        return count
    }
}

func main() {
    counter := outer()
    fmt.Println(counter()) // 输出：1
    fmt.Println(counter()) // 输出：2（状态被保留）
    
    newCounter := outer()  // 新闭包拥有独立状态
    fmt.Println(newCounter()) // 输出：1
}
```

### 实用场景：函数工厂

```
// 创建具有不同系数的乘法函数
func multiplier(factor int) func(int) int {
    return func(x int) int {
        return x * factor
    }
}

func main() {
    double := multiplier(2)  // 乘以2的函数
    triple := multiplier(3)  // 乘以3的函数
    
    fmt.Println(double(5))   // 输出：10
    fmt.Println(triple(5))   // 输出：15
}
```

### 注意事项

闭包捕获的是变量引用而非值，循环中使用需特别注意：

```
// 错误示例：所有闭包共享同一变量i
func wrongClosures() []func() {
    var funcs []func()
    for i := 0; i < 3; i++ {
        funcs = append(funcs, func() {
            fmt.Println(i) // 所有函数都输出3
        })
    }
    return funcs
}

// 正确示例：通过参数传递捕获当前值
func correctClosures() []func() {
    var funcs []func()
    for i := 0; i < 3; i++ {
        funcs = append(funcs, func(num int) func() {
            return func() {
                fmt.Println(num) // 分别输出0,1,2
            }
        }(i))
    }
    return funcs
}
```

## 6. 递归

递归是函数调用自身的编程技巧，适用于分治问题（如排序、搜索）和数学计算（如阶乘、斐波那契数列）。

### 基本结构

```
func 递归函数(参数) 返回值 {
    if 基线条件 {       // 终止递归的条件
        return 基线值
    }
    return 递归条件(参数变化) // 调用自身并缩小问题规模
}
```

### 阶乘计算示例

```
// 计算n的阶乘（n! = n × (n-1) × ... × 1）
func factorial(n int) int {
    if n <= 1 {         // 基线条件
        return 1
    }
    return n * factorial(n-1) // 递归调用
}

func main() {
    fmt.Println(factorial(5)) // 输出：120（5×4×3×2×1）
}
```

### 斐波那契数列

```
// 斐波那契数列：F(n) = F(n-1) + F(n-2)
func fibonacci(n int) int {
    if n <= 1 {
        return n // 基线条件：F(0)=0, F(1)=1
    }
    return fibonacci(n-1) + fibonacci(n-2)
}
```

### 注意事项

- **栈溢出风险**：递归深度过大会导致栈溢出，可通过尾递归优化（Go暂不支持自动优化）或转为迭代解决
- **效率问题**：重复计算（如斐波那契）可通过记忆化（缓存中间结果）优化

## 7. 延迟调用（defer）

`defer`语句用于延迟函数执行，确保资源释放等清理操作在函数退出前执行，无论函数正常返回还是发生错误。

### 基本特性

```
func main() {
    defer fmt.Println("最后执行")  // 3
    defer fmt.Println("中间执行")  // 2
    fmt.Println("最先执行")        // 1
    // 输出顺序：最先执行 → 中间执行 → 最后执行（后进先出）
}
```

### 资源释放最佳实践

```
// 文件操作示例
func readFile(path string) error {
    file, err := os.Open(path)
    if err != nil {
        return err
    }
    defer file.Close() // 确保文件关闭，即使读取失败也会执行

    // 读取文件内容...
    return nil
}
```

### 参数即时计算

`defer`函数的参数在声明时计算，而非执行时：

```
func main() {
    x := 10
    defer fmt.Println(x) // 输出10（声明时x的值）
    x = 20
}
```

## 8. 异常处理（panic/recover）

Go语言没有try-catch机制，通过`panic`触发异常，`recover`捕获异常，结合`defer`实现错误处理。

### 基本用法

```
func riskyOperation() (result int, err error) {
    defer func() {
        if r := recover(); r != nil { // 捕获panic
            err = fmt.Errorf("发生错误: %v", r)
            result = 0
        }
    }()

    if 1 == 1 {
        panic("模拟错误发生") // 触发异常
    }
    return 42, nil
}

func main() {
    res, err := riskyOperation()
    if err != nil {
        fmt.Println(err) // 输出：发生错误: 模拟错误发生
    } else {
        fmt.Println(res)
    }
}
```

### 最佳实践

- **谨慎使用panic**：仅用于不可恢复的错误（如程序启动失败），普通错误应返回error类型
- **recover必须在defer中使用**：直接调用recover无效
- **保持栈追踪**：捕获panic后如需重新抛出，可使用`panic(r)`

### 错误处理对比

```
// 推荐：返回error类型
func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, fmt.Errorf("除数不能为零") // 返回错误而非panic
    }
    return a / b, nil
}

// 不推荐：使用panic处理可恢复错误
func unsafeDivide(a, b int) int {
    if b == 0 {
        panic("除数不能为零") // 应仅用于致命错误
    }
    return a / b
}
```

## 9. 综合示例：函数式编程实践

```
// 实现一个简单的函数式数据处理管道
package main

import "fmt"

// 过滤函数
func filter(nums []int, predicate func(int) bool) []int {
    var result []int
    for _, num := range nums {
        if predicate(num) {
            result = append(result, num)
        }
    }
    return result
}

// 映射函数
func mapNumbers(nums []int, mapper func(int) int) []int {
    var result []int
    for _, num := range nums {
        result = append(result, mapper(num))
    }
    return result
}

// 归约函数
func reduce(nums []int, reducer func(int, int) int, initial int) int {
    result := initial
    for _, num := range nums {
        result = reducer(result, num)
    }
    return result
}

func main() {
    numbers := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
    
    // 数据处理管道：过滤偶数 → 平方 → 求和
    evenNumbers := filter(numbers, func(n int) bool {
        return n%2 == 0 // 保留偶数
    })
    
    squared := mapNumbers(evenNumbers, func(n int) int {
        return n * n // 平方运算
    })
    
    sum := reduce(squared, func(acc, n int) int {
        return acc + n // 累加求和
    }, 0)
    
    fmt.Println("结果:", sum) // 输出：220（2²+4²+6²+8²+10²=4+16+36+64+100）
}
```

## 10. 总结

函数是Go语言的核心构建块，本章介绍的八大特性覆盖了从基础定义到高级应用的全场景：

- **函数定义**：通过`func`关键字创建可复用逻辑单元
- **参数与返回值**：支持值传递、指针传递、可变参数和多返回值
- **匿名函数与闭包**：实现函数式编程和状态捕获
- **递归**：解决分治问题的优雅方案
- **延迟调用**：确保资源安全释放的最佳实践
- **异常处理**：通过`panic/recover`机制处理运行时错误

掌握这些特性将帮助你编写更简洁、高效、健壮的Go代码，为后续学习并发编程和服务端开发奠定基础。实际开发中，应根据场景选择合适的函数特性，遵循"简单优先"原则，避免过度使用高级特性导致代码可读性下降。

## 其他教程

<https://www.topgoer.com/%E5%87%BD%E6%95%B0/>
