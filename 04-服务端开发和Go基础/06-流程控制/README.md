# 流程控制

来源：
- https://campus.wps.cn/contentpreview/05d12ded-06d0-4797-82be-b6ba38c6f1a0

# Go语言流程控制

## 1. if 语句

### 基本语法与特性

Go 语言的 `if` 语句具有简洁性和灵活性，最显著的特点是**无需括号包裹条件表达式**，且支持**初始化语句**。

```
// 标准形式
if score >= 60 {
    fmt.Println("及格")
}

// 带初始化语句（常用场景）
if score := 75; score >= 60 {
    fmt.Printf("分数 %d，及格\n", score)
}
```

### 与其他语言的对比

| 特性 | Go | Java/Python |
| --- | --- | --- |
| 条件表达式括号 | 可选（推荐不使用） | 必须使用 |
| 初始化语句 | 支持在 if 中声明局部变量 | 需在外部声明变量 |
| 代码块强制要求 | 即使单语句也必须用大括号 | Python 可用缩进代替 |

### 注意事项

1. **左大括号必须与 if 同行**，否则会触发编译错误：

   ```
   // 错误示例
   if score > 100 
   { // 编译错误：左大括号必须在同一行
       fmt.Println("无效分数")
   }
   ```
2. **避免不必要的 else**：当 if 块中包含 return 语句时，else 可以省略

   ```
   // 推荐写法
   if err != nil {
       return err
   }
   // 后续逻辑...
   ```

## 2. switch 语句

### 核心特性

Go 的 `switch` 语句相比传统语言更加灵活，支持**任意类型的条件表达式**，且**默认自动 break**。

```
// 基本用法
day := 3
switch day {
case 1:
    fmt.Println("周一")
case 2, 3, 4: // 多值匹配
    fmt.Println("工作日")
case 5:
    fmt.Println("周五")
default:
    fmt.Println("周末")
}

// 表达式类型不限（字符串示例）
name := "Alice"
switch name {
case "Bob":
    fmt.Println("用户 Bob")
case "Alice":
    fmt.Println("用户 Alice")
}

// 无表达式（类似 if-else 链）
score := 85
switch {
case score >= 90:
    fmt.Println("优秀")
case score >= 60:
    fmt.Println("及格")
default:
    fmt.Println("不及格")
}
```

### 穿透控制与 fallthrough

使用 `fallthrough` 关键字可强制执行下一个 case（不判断条件）：

```
num := 2
switch num {
case 1:
    fmt.Println("1")
    fallthrough // 继续执行下一个 case
case 2:
    fmt.Println("2")
    fallthrough
case 3:
    fmt.Println("3") // 会被执行
}
// 输出：2\n3
```

### 与传统语言差异

1. **无需显式 break**：每个 case 执行完毕后自动退出 switch 块
2. **多值匹配**：单个 case 可匹配多个值（用逗号分隔）
3. **表达式可选**：可省略条件表达式，实现复杂条件判断
4. **类型断言场景**：常用于接口类型判断

   ```
   var x interface{} = "hello"
   switch v := x.(type) {
   case string:
       fmt.Printf("字符串类型：%s\n", v)
   case int:
       fmt.Printf("整数类型：%d\n", v)
   }
   ```

## 3. select 语句

### 专用场景

`select` 是 Go 特有的流程控制语句，**专门用于处理通道操作**，实现非阻塞的 I/O 多路复用。

```
ch1 := make(chan int)
ch2 := make(chan string)

go func() {
    ch1 <- 100
}()
go func() {
    ch2 <- "hello"
}()

// 随机选择一个就绪的通道
select {
case num := <-ch1:
    fmt.Printf("从 ch1 接收：%d\n", num)
case str := <-ch2:
    fmt.Printf("从 ch2 接收：%s\n", str)
case <-time.After(1 * time.Second): // 超时控制
    fmt.Println("超时")
default:
    fmt.Println("无就绪通道") // 非阻塞模式
}
```

### 关键特性

1. **随机性**：当多个 case 同时就绪时，随机选择一个执行（公平调度）
2. **阻塞性**：若所有 case 均未就绪且无 default，会阻塞当前 goroutine
3. **超时处理**：配合 `time.After` 实现通道操作超时控制
4. **空 select**：`select{}` 会导致当前 goroutine 永久阻塞（常用于优雅退出）

### 常见应用场景

- 实现非阻塞的通道读写
- 为通道操作设置超时
- 处理多个通道的并发事件
- 优雅退出 goroutine

## 4. for 循环

### 三种语法形式

Go 仅保留 `for` 一种循环关键字，但支持多种灵活形式：

1. **基本循环（类 C 风格）**

   ```
   for i := 0; i < 5; i++ {
       fmt.Println(i) // 输出 0-4
   }
   ```
2. **条件循环（类 while）**

   ```
   i := 0
   for i < 5 { // 仅保留条件判断
       fmt.Println(i)
       i++
   }
   ```
3. **无限循环**

   ```
   for { // 等价于 for ; ; {}
       fmt.Println("无限循环")
       break // 需配合 break 退出，否则死循环
   }
   ```

### 与其他语言的对比

| 语言 | 循环关键字 | 无限循环写法 |
| --- | --- | --- |
| Go | for（唯一） | `for {}` |
| Java | for/while/do-while | `while(true) {}` |
| Python | for/while | `while True:` |
| JavaScript | for/while/do-while | `while(true) {}` |

### 注意事项

- **缺少 while/do-while**：统一用 for 实现所有循环逻辑
- **循环变量作用域**：在循环内声明的变量仅在当前迭代有效

  ```
  for i := 0; i < 3; i++ {
      fmt.Println(&i) // 每次迭代的 i 是新变量（地址不同）
  }
  ```

## 5. range 遍历

### 多类型支持

`range` 关键字用于便捷遍历**数组、切片、映射、字符串、通道**等集合类型，返回索引/键和对应值。

```
// 1. 遍历数组/切片
nums := []int{10, 20, 30}
for index, value := range nums {
    fmt.Printf("索引 %d: 值 %d\n", index, value)
}

// 2. 遍历字符串（返回 rune 码点）
str := "Go语言"
for i, r := range str {
    fmt.Printf("索引 %d: 字符 %c (Unicode: %U)\n", i, r, r)
}
// 输出：
// 索引 0: 字符 G (Unicode: U+0047)
// 索引 1: 字符 o (Unicode: U+006F)
// 索引 2: 字符 语 (Unicode: U+8BED)
// 索引 5: 字符 言 (Unicode: U+8A00)

// 3. 遍历 map（键值对随机顺序）
m := map[string]int{"a": 1, "b": 2}
for key, val := range m {
    fmt.Printf("键 %s: 值 %d\n", key, val)
}

// 4. 遍历通道（接收数据直到通道关闭）
ch := make(chan int, 2)
ch <- 1
ch <- 2
close(ch)
for val := range ch { // 无需索引，直接取值
    fmt.Println(val) // 输出 1, 2
}
```

### 特殊用法

- **忽略索引/值**：使用 `_` 忽略不需要的返回值

  ```
  for _, val := range nums { // 仅关注值
      fmt.Println(val)
  }
  ```
- **遍历 map 顺序**：Go 1.21+ 引入 `slices.Sort` 可实现有序遍历

  ```
  keys := make([]string, 0, len(m))
  for k := range m {
      keys = append(keys, k)
  }
  slices.Sort(keys) // 需导入 "slices" 包
  for _, k := range keys {
      fmt.Printf("%s: %d\n", k, m[k])
  }
  ```

### 注意事项

1. **字符串遍历**：返回的索引是字节位置，可能非连续（如中文占 3 字节）
2. **map 随机性**：每次遍历顺序可能不同，避免依赖遍历顺序
3. **通道遍历**：仅能遍历接收通道，发送通道会导致死锁
4. **性能考量**：对大型切片，预分配索引变量可提升性能

   ```
   for i := 0; i < len(largeSlice); i++ { // 比 range 更高效
       val := largeSlice[i]
   }
   ```

## 6. 循环控制：Goto、Break、Continue

### 标签与嵌套控制

Go 提供标签（Label）机制，配合循环控制语句实现复杂流程跳转：

```
outer: // 定义外层循环标签
for i := 0; i < 3; i++ {
    for j := 0; j < 3; j++ {
        if j == 1 {
            continue outer // 跳转到 outer 标签继续外层循环
        }
        fmt.Printf("i=%d, j=%d\n", i, j)
    }
}
// 输出：
// i=0, j=0
// i=1, j=0
// i=2, j=0
```

### Goto 语句

用于在函数内跳转到指定标签，**谨慎使用**以避免代码混乱：

```
func findElement(nums []int, target int) int {
    for i, v := range nums {
        if v == target {
            goto found // 跳转到 found 标签
        }
    }
    return -1 // 未找到
found:
    return i // 此处可访问循环内的 i 变量
}
```

### 控制语句对比表

| 语句 | 作用范围 | 配合标签效果 | 典型应用场景 |
| --- | --- | --- | --- |
| break | 退出当前循环/switch/select | 退出标签指定的外层循环 | 提前终止多层循环 |
| continue | 跳过当前循环迭代 | 跳过标签指定循环的当前迭代 | 跳过外层循环的当前迭代 |
| goto | 跳转到函数内标签 | 无（直接跳转） | 统一错误处理、简化嵌套逻辑 |

### 最佳实践

1. **禁止跨函数跳转**：goto 仅能在当前函数内使用
2. **避免过度使用 goto**：优先用函数返回代替复杂 goto 跳转
3. **标签命名规范**：使用有意义的标签名（如 `retry:`、`outerLoop:`）
4. **break 与 switch**：在 switch 中使用 break 需注意作用域

   ```
   switch {
   case true:
       fmt.Println("case 1")
       break // 仅退出 switch，不影响外层循环
   }
   ```

## 7. 综合对比与最佳实践

### 控制流语句选择指南

| 场景需求 | 推荐语句 | 替代方案 |
| --- | --- | --- |
| 简单条件判断 | if | - |
| 多值匹配 | switch | 多个 if-else |
| 通道事件处理 | select | 无（Go 特有） |
| 固定次数循环 | for (基本形式) | - |
| 集合遍历 | for range | for + 索引 |
| 无限循环/条件循环 | for {} / for 条件 | - |
| 多层循环控制 | break/continue + 标签 | 重构为函数返回 |
| 错误处理跳转 | goto | 函数提前返回 |

### 避坑指南

1. **for range 迭代变量复用**：循环内保存迭代变量地址会导致全部指向最后一个值

   ```
   // 错误示例
   nums := []int{1, 2, 3}
   var ptrs []*int
   for _, v := range nums {
       ptrs = append(ptrs, &v) // 所有元素都指向同一地址
   }
   // 修复：每次迭代创建新变量
   for _, v := range nums {
       val := v // 局部变量
       ptrs = append(ptrs, &val)
   }
   ```
2. **select 空 default 风险**：可能导致 CPU 空转

   ```
   // 不推荐（高 CPU 占用）
   for {
       select {
       case <-ch:
           // 处理数据
       default: // 无数据时立即执行
       }
   }
   // 推荐（添加短暂休眠）
   for {
       select {
       case <-ch:
           // 处理数据
       default:
           time.Sleep(10 * time.Millisecond) // 降低 CPU 占用
       }
   }
   ```
3. **switch 表达式类型一致性**：case 表达式类型必须与 switch 条件类型匹配

   ```
   // 错误示例
   switch 10 { // int 类型
   case "10": // string 类型，编译错误
       fmt.Println("错误")
   }
   ```

通过本章学习，我们掌握了 Go 语言流程控制的核心语法与最佳实践。Go 以简洁统一的控制流设计，在保留灵活性的同时大幅降低了语法复杂度，尤其在并发编程（select）和集合处理（range）方面展现了独特优势。实际开发中，应根据具体场景选择合适的控制语句，并遵循 Go 社区的简洁风格原则。
