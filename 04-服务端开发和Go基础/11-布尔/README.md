# 布尔

来源：
- https://campus.wps.cn/contentpreview/b03f6533-c240-4fce-bf5c-c17ccf5fab6c

# 数据类型：布尔类型 (Boolean)

布尔类型（`bool`）是 Go 语言中最简单的数据类型之一，用于表示逻辑状态，即真或假。

## 一、核心特性

| 特性 | 描述 |
| --- | --- |
| **关键字** | 使用关键字 `bool` 定义。 |
| **取值范围** | 只有两个预定义常量值：`true`（真）和 `false`（假）。 |
| **零值** | 布尔变量的默认零值是 `false`。 |
| **不可转换** | 布尔类型不能与任何其他数据类型（如整型 `int` 或 `float`）进行相互转换。 |

## 二、定义与初始化

### 1. 变量声明

```
package main

import "fmt"

func main() {
    // 方式一：默认零值初始化 (false)
    var isRaining bool 
    fmt.Println("默认值:", isRaining) // 输出: 默认值: false

    // 方式二：声明时赋值
    var isSunny bool = true
    fmt.Println("赋值:", isSunny) // 输出: 赋值: true

    // 方式三：短变量声明（推荐）
    isLoggedIn := false
    fmt.Println("短变量声明:", isLoggedIn) // 输出: 短变量声明: false
}
```

### 2. 布尔常量

Go 语言内置了两个布尔常量：`true` 和 `false`。

```
const Active = true
const Inactive = false
```

## 三、逻辑操作符

布尔类型主要用于逻辑判断，通常与逻辑操作符一起使用：

| 操作符 | 名称 | 描述 | 示例 |
| --- | --- | --- | --- |
| `&&` | 逻辑与 (AND) | 只有当左右两边的表达式都为 `true` 时，结果才为 `true`。 | `true && false` 结果是 `false` |
| ` |  | ` | 逻辑或 (OR) |
| `!` | 逻辑非 (NOT) | 对布尔值取反。 | `!true` 结果是 `false` |

### 短路求值

Go 语言的逻辑与 (`&&`) 和逻辑或 (`||`) 采用短路求值：

1. 对于 `A && B`：如果 `A` 已经是 `false`，那么 `B` **不会被执行**，因为无论 `B` 是什么，结果都已经是 `false`。
2. 对于 `A || B`：如果 `A` 已经是 `true`，那么 `B` **不会被执行**，因为无论 `B` 是什么，结果都已经是 `true`。

这种特性常用于在执行某个操作前进行安全检查，以避免运行时错误。

```
package main

import "fmt"

func main() {
    userExists := true
    isAdmin := false

    // 逻辑与 (&&) 示例
    if userExists && isAdmin {
        fmt.Println("用户是存在的管理员")
    } else {
        fmt.Println("用户不是存在的管理员") // 输出此行
    }

    // 短路示例：如果 map 是 nil，user["age"] 可能会 panic。
    // 确保 map != nil 优先执行。
    var data map[string]int
    if data != nil && data["count"] > 0 {
        fmt.Println("数据有效")
    } else {
        fmt.Println("数据无效或为空") // data!=nil 为 false，短路，不访问 data["count"]
    }
}
```

## 四、在流程控制中的应用

布尔类型是所有流程控制语句（`if`、`for`、`switch`）的基础。

### 1. `if` 条件判断

`if` 语句后的条件表达式必须是布尔类型或返回布尔类型的表达式。

```
age := 20
isAdult := age >= 18

if isAdult { // 直接使用布尔变量
    fmt.Println("是成年人")
}

if 10 > 5 { // 比较运算的结果是布尔值
    fmt.Println("10 大于 5")
}
```

### 2. 与比较操作符结合

布尔值通常是比较操作符（`==`, `!=`, `>`, `<`, `>=`, `<=`) 运算的结果。

```
isEqual := (5 == 5) // true
isNotEqual := (5 != 6) // true
```

### 3. **禁止数字与布尔互转**

与其他一些语言（如 C 语言中 0/非 0）不同，Go 语言**严格禁止**将数字类型直接当作布尔值使用，反之亦然。

```
// ❌ 错误示例：不能将 int 直接用于 if 条件
// if 1 { 
//     // 编译错误: non-boolean (type int) used as a condition
// }

// ❌ 错误示例：不能将 true 赋值给 int
// var x int = true 
// 编译错误: cannot use true (type bool) as type int in assignment
```

如果你需要根据数字的零/非零状态进行判断，必须使用显式的比较操作：

```
count := 10
if count != 0 {
    // 正确：使用比较表达式
    fmt.Println("count 不为 0")
}
```
