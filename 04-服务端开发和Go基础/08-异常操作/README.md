# 异常操作

来源：
- https://campus.wps.cn/contentpreview/d6cd1826-2065-4d30-b9ec-14a6bf71d951

# 异常机制panic和recover

# Go 语言的异常处理机制

Go 语言通过`defer`、`panic`和`recover`三个关键字构建了一种独特的异常处理机制。它们协同工作，使得 Go 程序能够优雅地处理运行时错误和异常情况。本文将深入浅出地解析这三个关键字的用法、特点以及常见问题与易错点，并通过代码示例进行演示。

## Defer 语句

`defer`语句用于延迟执行一个函数调用，直到包含该`defer`语句的函数返回时才执行。这在资源释放、日志记录等场景中尤为有用。

### defer 的基本用法

```
package main

import "fmt"

func main() {
    defer fmt.Println("Closing file...")
    // 执行文件操作...
}
// 输出：Closing file...
```

### defer 的执行顺序

如果有多个`defer`语句，它们按后进先出（LIFO）顺序执行：

```
package main

import "fmt"

func main() {
    defer fmt.Println("Second deferred call")
    defer fmt.Println("First deferred call")
    // 执行其他操作...
}
// 输出：
// First deferred call
// Second deferred call
```

### defer 的实际应用场景

1. 文件操作

```
package main

import (
    "fmt"
    "os"
)

func readFile(filename string) error {
    file, err := os.Open(filename)
    if err != nil {
        return err
    }
    defer file.Close() // 确保文件最终被关闭

    // 文件操作...
    return nil
}
```

2. 数据库连接

```
package main

import (
    "database/sql"
    "fmt"
)

func queryDB() error {
    db, err := sql.Open("mysql", "user:password@tcp(127.0.0.1:3306)/dbname")
    if err != nil {
        return err
    }
    defer db.Close() // 确保数据库连接被关闭

    // 数据库操作...
    return nil
}
```

### defer 的注意事项

1. defer 的参数求值时机

defer 的参数在 defer 语句执行时求值，而不是在实际执行时求值。

```
package main

import "fmt"

func main() {
    i := 0
    defer fmt.Println("deferred value:", i) // 会打印0，而不是1
    i++
    fmt.Println("current value:", i)
}
```

1. defer 在循环中的使用

```
package main

import "fmt"

func wrong() {
    for i := 0; i < 3; i++ {
        defer fmt.Println(i) // 不推荐在循环中直接使用defer
    }
}

func right() {
    for i := 0; i < 3; i++ {
        i := i // 创建新的变量
        defer func() {
            fmt.Println(i)
        }()
    }
}
```

## 小练习 - defer

1. 编写一个函数，使用`defer`语句在函数结束时打印一条日志信息。
2. 修改上述代码，添加多个`defer`语句，观察它们的执行顺序。
3. 实现一个简单的性能计时器，使用`defer`和`time.Now()`记录函数执行时间。

## Panic 语句

`panic`语句用于触发一个运行时错误，立即停止当前函数的执行，并开始回溯调用栈。在 Go 中，`panic`主要用于处理真正的异常情况，而不是普通的错误。

### panic 的定义

```
package main

func main() {
    f()
}

func f() {
    panic("发生了一个panic")
}
```

### panic 的使用场景

1. 初始化失败

一般这种情况下，可能导致系统无法启动，所以需要使用 panic 来立即停止程序。

```
package main

func initConfig() {
    config, err := loadConfig()
    if err != nil {
        panic("无法加载配置文件，系统无法启动")
    }
}
```

2. 不可恢复的错误

```
package main

import "net/http"

func startServer(port string) {
    if err := http.ListenAndServe(port, nil); err != nil {
        panic(fmt.Sprintf("服务器启动失败: %v", err))
    }
}
```

### panic 的最佳实践

1. 区分错误和异常

```
package main

import "errors"

func processData(data []int) error {
    if len(data) == 0 {
        return errors.New("空数据") // 普通错误，返回error
    }

    if data == nil {
        panic("空指针异常") // 真正的异常情况
    }

    return nil
}
```

2. 自定义 panic 信息

```
package main

type CustomError struct {
    Code    int
    Message string
}

func (e *CustomError) Error() string {
    return fmt.Sprintf("错误码: %d, 信息: %s", e.Code, e.Message)
}

func process() {
    panic(&CustomError{
        Code:    500,
        Message: "严重错误",
    })
}
```

上面的示例中，我们定义了一个自定义的错误类型 `CustomError`，并实现了 `Error` 方法，所以 CustomError 可以被当作 error 类型使用。当 `process` 函数触发 panic 时，会创建一个 `CustomError` 实例，并将其作为参数传递给 `panic` 函数。

## Recover 函数

`recover`函数用于捕获和处理 panic，从而阻止 panic 继续传播，使程序可以继续正常执行。只能在 defer 函数中使用。

### recover 的基本用法

```
package main

import "fmt"

func handlePanic() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Printf("Recovered from: %v\n", r)
        }
    }()
    panic("发生异常")
}
```

### recover 的高级应用

1. 错误转换和处理

```
package main

import (
    "fmt"
    "log"
)

func convertPanicToError() (err error) {
    defer func() {
        if r := recover(); r != nil {
            switch x := r.(type) {
            case string:
                err = errors.New(x)
            case error:
                err = x
            default:
                err = fmt.Errorf("未知错误: %v", x)
            }
            log.Printf("从panic中恢复: %v", err)
        }
    }()

    // 可能发生panic的代码
    panic("一些错误")
    return nil
}
```

# 一个案例

```
package main

import "fmt"

func divide(a, b int) int {
	if b == 0 {
		panic("除数不能为零")
	}
	return a / b
}

func main() {
	defer func() {
		if err := recover(); err != nil {
			fmt.Println("发生异常:", err)
		}
	}()
	result := divide(10, 0)
	fmt.Println("结果:", result)

	result = divide(10, 2)
	fmt.Println("结果:", result)
}
```

recover 可以捕获并处理 panic，但它并不能让函数从发生 panic 的地方继续执行。当 panic 发生时：

- 当前函数立即停止正常执行
- defer 函数会被执行
- 在 defer 函数中使用 recover 可以捕获 panic 信息
- 即使成功 recover，当前函数也会提前返回，不会继续执行 panic 点之后的代码  
  在代码中，divide(10, 0)触发 panic 后，虽然 defer 中的 recover 捕获了异常，但 main 函数已经无法继续执行到 divide(10, 2)那一行了。  
  如果想要让两次除法都执行，需要调整代码结构，例如将每次除法操作放在单独的函数中

## 最佳实践总结

注意：recover 和 defer 并不能取代传统错误处理，而是应该用于处理异常情况。

1. defer 的使用原则：

   - 在获取资源后立即使用 defer 释放
   - 注意 defer 的性能开销
   - 避免在循环中使用 defer
2. panic 的使用原则：

   - 只在真正的异常情况下使用 panic
   - 在程序初始化阶段，遇到致命错误时使用 panic
   - 对可恢复的错误使用 error 返回值
3. recover 的使用原则：

   - 总是在 defer 函数中调用 recover
   - 对捕获的 panic 进行适当的处理和转换
   - 在合适的层级处理 panic

# 其他教程

[https://www.topgoer.com/函数/异常处理](https://www.topgoer.com/%E5%87%BD%E6%95%B0/%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86.html)

<https://go.sixue.work/golang/c01/c01_15.html>
