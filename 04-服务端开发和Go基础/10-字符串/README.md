# 字符串

来源：
- https://campus.wps.cn/contentpreview/5d6cd391-1894-491c-8e85-5116b16ed587

# 数据类型：byte、rune与字符串

Go 语言中的字符串是一种原生（内置）数据类型，与其他基本类型（如 `int`、`bool`）一样，使用起来非常方便。理解 Go 字符串的核心在于理解它与 **UTF-8 编码**以及 **`byte` 和 `rune` 字符类型**的关系。

## 一、字符串的定义与基本特性

### 1. 核心特性

| 特性 | 描述 |
| --- | --- |
| **不可变性** | 字符串一旦创建，就不能被修改。修改字符串必须创建新的字符串。 |
| **底层实现** | 字符串的本质是一个只读的 `byte` 字节切片 (`[]byte`)。 |
| **编码** | Go 语言的字符串默认使用 **UTF-8 编码**。 |
| **零值** | 字符串变量的默认零值是空字符串 `""`。 |

### 2. 字符串的声明与表示

#### (1) 申明字符串（双引号 `""`）

Go 语言里的字符串的内部实现使用UTF-8编码。 字符串的值为双引号(")中的内容，可以在Go语言的源码中直接添加非ASCII码字符，例如：

```
s1 := "hello"
s2 := "你好"
fmt.Println(s1)
```

#### (2) 多行字符串（反引号 ``）

Go语言中要定义一个多行字符串时，就必须使用反引号字符：

```
s1 := `第一行
    第二行
    第三行
`
fmt.Println(s1)
```

### 3. 字符串转义符

Go语言支持多种转义字符来表示特殊字符：

| 转义符 | 含义 |
| --- | --- |
| `\n` | 换行符 |
| `\r` | 回车符 |
| `\t` | 制表符 |
| `\'` | 单引号 |
| `\"` | 双引号 |
| `\\` | 反斜杠 |

示例：

```
s := "这是第一行\n这是第二行"
fmt.Println(s)
s2 := "Tab:\t缩进"
fmt.Println(s2)
s3 := "C:\\Windows\\System32"  // 用\\表示一个\
fmt.Println(s3)
```

## 二、字符类型：`byte` 与 `rune`

由于 Go 字符串使用 UTF-8 编码，单个字符可能占用 1 到 4 个字节，因此 Go 提供了两种字符类型来处理不同的编码需求。

### 1. `byte` 类型

- **本质：** `byte` 是 `uint8` 的别名。
- **大小：** 占用 **1 个字节**（8 位）。
- **用途：** 主要用来表示 **ASCII 字符**（如英文字母、数字和常见符号），因为 ASCII 字符只占用 1 个字节。
- **字面量：** 使用单引号 `'` 包裹。

```
var a byte = 'A' // ASCII 码 65
var b uint8 = 66 // byte 和 uint8 本质相同

fmt.Printf("%c\n", a) // 输出: A
```

### 2. `rune` 类型

- **本质：** `rune` 是 `int32` 的别名。
- **大小：** 占用 **4 个字节**（32 位）。
- **用途：** 主要用来表示 **Unicode 字符**（即 UTF-8 编码中的一个字符，包括中文、日文、表情符号等）。
- **字面量：** 同样使用单引号 `'` 包裹。

```
var chineseChar rune = '中' // 中文汉字需要 3 个字节存储，但用 4 字节的 rune 表示

fmt.Printf("%c\n", chineseChar) // 输出: 中
```

```
import (
	"fmt"
	"unsafe"
)

func main() {
	var a byte = 'A'
	var b rune = 'B'
	fmt.Printf("a 占用 %d 个字节数\nb 占用 %d 个字节数", unsafe.Sizeof(a), unsafe.Sizeof(b))
}

输出如下：
a 占用 1 个字节数
b 占用 4 个字节数
```

> **总结：** 在 Go 中，单引号 `'A'` 表示一个**字符**（`byte` 或 `rune`），双引号 `"A"` 表示一个**字符串**（`string`）。

## 三、字符串与字符的遍历与长度

### 1. 长度：`len()`

内置函数 `len()` 返回的是字符串包含的**字节数（Byte Count）**，而不是字符数。

```
s := "hello,中国"
// 'h','e','l','l','o',',' 占 6 字节
// '中','国' 各占 3 字节 (UTF-8)
fmt.Println("字节长度:", len(s)) // 输出: 12
```

### 2. 按字节遍历

使用索引或普通的 `for` 循环遍历字符串，获取的是组成字符串的**原始字节**。如果字符串包含中文等多字节字符，会产生乱码。

```
s := "你好"
for i := 0; i < len(s); i++ {
    // 打印每个字节的数值和对应的字符（可能会乱码）
    fmt.Printf("%v(%c) ", s[i], s[i]) 
}
// 输出（示例）：228(å) 189(½) 160( ) 229(å) 165(¥) 189(½)
```

### 3. 按字符遍历

使用 `for range` 循环遍历字符串时，Go 会自动处理 UTF-8 编码，每次迭代返回的是一个 **`rune` 类型**的字符和一个**字符起始位置的索引**。

```
s := "你好，世界"
for index, char := range s {
    // index 是字节索引，char 是 rune 类型 (Unicode 字符)
    fmt.Printf("索引:%d, 字符:%c\n", index, char)
}
// 输出：
// 索引:0, 字符:你 (中文字符，占 3 个字节)
// 索引:3, 字符:好
// ...
```

> **提示：** 如果需要获取正确的**字符数**，应将字符串转换为 `[]rune` 后再获取长度：`len([]rune(s))`。

## 四、字符串的修改与类型转换

由于字符串是不可变的，如果需要修改字符串内容，必须先将其转换为可变的类型（如字节切片或 `rune` 切片），修改后再转换回字符串。

### 1. 修改英文字符

适用于只包含 ASCII 字符的场景，效率较高。

```
s := "hello"
// 1. 转换为 []byte
byteSlice := []byte(s) 
// 2. 修改第一个元素
byteSlice[0] = 'H' 
// 3. 转换回 string
newS := string(byteSlice) 
fmt.Println(newS) // 输出: Hello
```

### 2. 修改多字节字符

处理包含中文等 Unicode 字符时，**必须**使用 `[]rune` 转换，以避免破坏 UTF-8 编码结构。

```
s2 := "博客"
// 1. 转换为 []rune (处理 Unicode 字符)
runeSlice := []rune(s2) 
// 2. 修改第一个字符
runeSlice[0] = '狗' 
// 3. 转换回 string
newS2 := string(runeSlice)
fmt.Println(newS2) // 输出: 狗客
```

### 3. 字符串与数值类型的转换

字符串和数值（`int`, `float`, `bool`）之间的转换，不能直接使用类型转换语法（如 `int("123")`），必须依赖标准库 `strconv` 包提供的函数。

#### (1) 数值转字符串

| 函数 | 描述 | 示例 |
| --- | --- | --- |
| `strconv.Itoa(i int)` | 将整型（`int`）转换为字符串。 | `strconv.Itoa(123)` -> `"123"` |
| `strconv.FormatInt(i, base)` | 将整型转换为指定进制（`base`）的字符串。 | `strconv.FormatInt(15, 16)` -> `"f"` |
| `strconv.FormatFloat(f, fmt, prec, bitSize)` | 将浮点型转换为字符串。 | `strconv.FormatFloat(3.14, 'f', 2, 64)` -> `"3.14"` |

```
import "strconv"

num := 42
s := strconv.Itoa(num)
fmt.Printf("int 转 string: %s, 类型: %T\n", s, s) // 42, string
```

#### (2) 字符串转数值

字符串转数值可能会失败（例如试图将 "abc" 转为整数），因此这些函数通常返回两个值：结果和错误。

| 函数 | 描述 | 示例 |
| --- | --- | --- |
| `strconv.Atoi(s string)` | 将字符串转换为整型（`int`）。 | `strconv.Atoi("123")` -> `123, nil` |
| `strconv.ParseInt(s, base, bitSize)` | 将字符串转换为指定大小和进制的整型。 | `strconv.ParseInt("ff", 16, 64)` -> `255, nil` |
| `strconv.ParseFloat(s, bitSize)` | 将字符串转换为浮点型。 | `strconv.ParseFloat("3.14", 64)` -> `3.14, nil` |

```
import "strconv"

s := "12345"
i, err := strconv.Atoi(s)

if err != nil {
    fmt.Println("转换失败:", err)
} else {
    fmt.Printf("string 转 int: %d, 类型: %T\n", i, i) // 12345, int
}
```

## 五、字符串的常用操作

Go 语言标准库 `strings` 包提供了大量的字符串操作函数。

| 函数 | 描述 | 示例 |
| --- | --- | --- |
| `len(s)` | 返回字符串的字节长度。 | `len("go")` -> 2 |
| `s1 + s2` | 拼接字符串。 | `"go" + "lang"` -> "golang" |
| `strings.Split(s, sep)` | 分割字符串，返回切片。 | `strings.Split("a,b,c", ",")` -> `["a", "b", "c"]` |
| `strings.Join(s, sep)` | 连接切片元素，返回字符串。 | `strings.Join([]string{"a","b"}, "-")` -> "a-b" |
| `strings.Contains(s, substr)` | 判断是否包含子串。 | `strings.Contains("golang", "go")` -> `true` |
| `strings.HasPrefix(s, prefix)` | 判断是否以前缀开头。 | `strings.HasPrefix("hello", "he")` -> `true` |
| `strings.Index(s, substr)` | 查找子串第一次出现的位置（字节索引）。 | `strings.Index("golang", "a")` -> 3 |
| `fmt.Sprintf` | 格式化拼接字符串（常用于复杂拼接）。 | `fmt.Sprintf("%s版本%d", "Go", 1)` -> "Go版本1" |

## 六、字符串的底层与内存

理解字符串的底层结构有助于我们写出更高效的 Go 代码。

### 1. 字符串的底层结构

在 Go 语言内部，`string` 类型实际上是一个轻量级的结构体，它包含两个字段：

1. **指向底层字节数组（`[]byte`）的指针：** 指向存储字符串数据的内存地址。
2. **长度（Length）：** 存储字符串的字节数（不是字符数）。

由于字符串和其底层的字节数组是分开存储的，**修改字符串**意味着创建新的字节数组和新的字符串结构体，这解释了字符串的不可变性。

### 2. 字符串与切片/数组的转换开销

将 `string` 与 `[]byte` 或 `[]rune` 相互转换，会涉及**内存重新分配和数据复制**。

| 转换操作 | 描述 | 内存开销 | 性能考虑 |
| --- | --- | --- | --- |
| `string(b)` （`[]byte` 转 `string`） | **复制**字节切片内容到新分配的字符串内存中。 | 较高 | 频繁转换会降低性能。 |
| `[]byte(s)` （`string` 转 `[]byte`） | **复制**字符串内容到新分配的字节切片内存中。 | 较高 | 如果只是读取，尽量使用 `s[i]` 索引。 |
| `[]rune(s)` （`string` 转 `[]rune`） | **复制**并进行 **UTF-8 解码**，将每个字符存储为 4 字节的 `rune`。 | 很高 | 仅在需要处理多字节字符时使用。 |

**最佳实践：** 尽量避免在紧密的循环中频繁进行字符串和切片之间的转换。如果只是进行字符串构建，推荐使用 `strings.Builder`。

### 3. 字符串拼接效率

在 Go 语言中，使用不同的方式拼接字符串，效率有巨大的差别：

| 拼接方式 | 性能特点 | 推荐场景 |
| --- | --- | --- |
| **`+` 操作符** | 每次拼接都会创建新的字符串和新的底层数组，效率最低（尤其是大量循环）。 | 少量、固定的字符串拼接。 |
| **`fmt.Sprintf`** | 依赖反射和格式化，效率一般。 | 需要复杂的格式化输出时。 |
| **`strings.Join`** | 内部预估容量，只进行一次分配和复制，效率较高。 | 拼接一个已知元素的 `[]string` 切片时。 |
| **`strings.Builder`** | 专为构建字符串设计，可以预估容量并减少内存分配次数，**效率最高**。 | 循环内进行大量字符串拼接时。 |

**`strings.Builder` 示例：**

```
import "strings"

func buildString(parts []string) string {
    var builder strings.Builder
    // 预估容量，减少内存重分配
    builder.Grow(100) 
    
    for _, p := range parts {
        builder.WriteString(p)
    }
    return builder.String()
}
```

## 七、字符串的格式化输出

`fmt` 包提供了一系列格式化输出的动词（Verb），尤其在处理字符串时非常有用。

| 格式化动词 | 描述 | 示例 |
| --- | --- | --- |
| **`%s`** | 输出字符串。 | `fmt.Printf("%s", "hello")` |
| **`%q`** | 输出带**双引号**的字符串，并对特殊字符进行转义（**解释型表示法**）。 | `fmt.Printf("%q", "a\n")` -> `"a\n"` |
| **`%c`** | 输出 Unicode 字符。 | `fmt.Printf("%c", '中')` |
| **`%v`** | 输出值的默认格式。 |  |
| **`%#v`** | 输出值的 Go 语法表示，常用于调试结构体。 |  |

```
package main

import "fmt"

func main() {
    path := "C:\temp\file.txt"
    
    // %s: 直接输出
    fmt.Printf("路径原始输出: %s\n", path) 
    
    // %q: 输出解释型字符串（带引号和转义）
    fmt.Printf("路径带引号输出: %q\n", path) 
    
    // %c: 打印 rune 字符
    fmt.Printf("第一个字符: %c\n", path[0]) // 注意：path[0] 是 byte，但 %c 仍可将其作为字符打印
}
```
