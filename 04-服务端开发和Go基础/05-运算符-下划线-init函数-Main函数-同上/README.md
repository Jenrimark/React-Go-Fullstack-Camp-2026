# 运算符，下划线，init函数，Main函数（同上）

来源：
- https://campus.wps.cn/contentpreview/7c70cc86-94ca-439b-b089-278ba3d6d311

说明：
- 该目录在总目录中标注“同上”，正文从共享页面中按对应小节拆出。

# 3. Init函数和main函数

Go程序的执行入口是`main`包中的`main()`函数，但在`main()`执行前会先执行所有`init()`函数。

## 3.1 init函数特性

- **无参数无返回值**：`func init() {}`
- **自动执行**：无需显式调用，由Go运行时自动调用
- **执行顺序**：
  1. 同包内多个init函数按**出现顺序**执行
  2. 不同包按**导入依赖顺序**执行（被依赖包先执行）
  3. 所有init函数执行完毕后，才执行`main()`函数

```
// 包初始化示例（文件名：demo.go）
package main

import "fmt"

// 第一个init函数
func init() {
    fmt.Println("init 1 executed")
}

// 第二个init函数
func init() {
    fmt.Println("init 2 executed")
}

func main() {
    fmt.Println("main executed")
}

// 输出顺序：
// init 1 executed
// init 2 executed
// main executed
```

## 3.2 main函数规则

- 必须声明在`main`包中：`package main`
- 无参数无返回值：`func main() {}`
- 程序入口唯一：一个项目只能有一个`main()`函数
- 命令行参数通过`os.Args`获取：

```
package main

import (
    "fmt"
    "os"
)

func main() {
    fmt.Println("命令行参数：", os.Args) // os.Args[0]为程序名
}
```

# 4. 运算符与下划线

## 4.1 运算符分类

Go语言支持**5类运算符**，优先级从高到低为：

| 类别 | 运算符示例 | 说明 |
| --- | --- | --- |
| 算术运算符 | `+`, `-`, `*`, `/`, `%` | 取余运算结果符号与被除数一致 |
| 比较运算符 | `==`, `!=`, `<`, `>`, `<=`, `>=` | 返回布尔值 |
| 逻辑运算符 | `&&`, ` |  |
| 位运算符 | `&`, ` | `,` ^`, `<<`, `>>` |
| 赋值运算符 | `=`, `+=`, `-=`, `*=` | 复合赋值 |

> 💡 技巧：不确定优先级时，使用括号明确运算顺序，如`(a + b) * c`

## 4.2 下划线（空白标识符）

下划线`_`是Go的特殊标识符，用于**忽略不需要的值**，主要场景：

1. **忽略函数返回值**：

```
_, err := os.Open("file.txt") // 只关心错误，忽略文件句柄
if err != nil { /* 处理错误 */ }
```

2. **导入包仅执行init**：

```
import _ "github.com/lib/pq" // 执行pq包的init函数，注册数据库驱动
```

3. **忽略循环索引**：

```
for _, v := range []string{"a", "b"} { // 只需要值，不需要索引
    fmt.Println(v)
}
```

> ⚠️ 注意：下划线不能作为变量使用，如`_ = 10`是合法的（忽略值），但`_++`会报错
