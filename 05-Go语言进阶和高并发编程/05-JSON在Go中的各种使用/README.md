# JSON在Go中的各种使用

来源：
- https://campus.wps.cn/contentpreview/d63c1203-0bc4-4304-8288-5583a28b2396

# JSON在Go中的各种使用

在Go语言中，JSON（JavaScript Object Notation）是一种常用的数据格式，用于在不同系统之间进行数据交换。Go语言提供了标准库 `encoding/json`，用于处理JSON数据。

## 一、JSON基本概念

JSON是一种轻量级的数据交换格式，易于阅读和编写，同时也易于机器解析和生成。它基于JavaScript编程语言的一个子集，但JSON是独立于语言的文本格式，并且采用了类似于C语言家族的习惯（包括C、C++、C#、Java、JavaScript、Perl、Python等）。

JSON数据由键值对组成，其中键是字符串，值可以是以下几种类型之一：

- 字符串（String）
- 数字（Number）
- 布尔值（Boolean）
- 数组（Array）
- 对象（Object）
- null

以下是一个简单的JSON示例：

```
{
  "name": "John Doe",
  "age": 30,
  "isStudent": false,
  "hobbies": ["reading", "writing", "coding"],
  "address": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  }
}
```

## 二、Go语言中处理JSON数据

### （一）导入 `encoding/json` 包

在使用Go语言处理JSON数据之前，需要先导入 `encoding/json` 包。

```
import "encoding/json"
```

### （二）将Go结构体转换为JSON格式

在Go语言中，可以使用结构体来表示JSON数据。通过使用 `json.Marshal` 函数，可以将Go结构体转换为JSON格式的字节切片。

下面是一个示例代码：

```
package main

import (
    "encoding/json"
    "fmt"
)

// 定义一个结构体
type Person struct {
    Name    string   `json:"name"`
    Age     int      `json:"age"`
    Hobbies []string `json:"hobbies"`
}

func main() {
    // 创建一个Person结构体实例
    person := Person{
        Name:    "John Doe",
        Age:     30,
        Hobbies: []string{"reading", "writing", "coding"},
    }

    // 将Person结构体转换为JSON格式的字节切片
    jsonData, err := json.Marshal(person)
    if err!= nil {
        fmt.Println("Error:", err)
        return
    }

    // 输出JSON数据
    fmt.Println(string(jsonData))
}
```

在上述代码中，定义了一个 `Person` 结构体，包含 `Name`、`Age` 和 `Hobbies` 三个字段。通过使用 `json.Marshal` 函数，将 `Person` 结构体转换为JSON格式的字节切片。如果转换过程中发生错误，会打印错误信息并返回。最后，将JSON格式的字节切片转换为字符串并输出。

需要注意的是，在结构体字段上使用了 `json:"name"` 等标签，用于指定结构体字段在JSON中的名称。如果不指定标签，默认使用结构体字段的名称作为JSON中的键。

### （三）将JSON格式转换为Go结构体

在Go语言中，可以使用 `json.Unmarshal` 函数将JSON格式的字节切片转换为Go结构体。

下面是一个示例代码：

```
package main

import (
    "encoding/json"
    "fmt"
)

// 定义一个结构体
type Person struct {
    Name    string   `json:"name"`
    Age     int      `json:"age"`
    Hobbies []string `json:"hobbies"`
}

func main() {
    // JSON数据
    jsonData := `{"name":"John Doe","age":30,"hobbies":["reading","writing","coding"]}`

    // 将JSON格式的字节切片转换为Person结构体
    var person Person
    err := json.Unmarshal([]byte(jsonData), &person)
    if err!= nil {
        fmt.Println("Error:", err)
        return
    }

    // 输出Person结构体的字段值
    fmt.Println("Name:", person.Name)
    fmt.Println("Age:", person.Age)
    fmt.Println("Hobbies:", person.Hobbies)
}
```

在上述代码中，定义了一个 `Person` 结构体，包含 `Name`、`Age` 和 `Hobbies` 三个字段。通过使用 `json.Unmarshal` 函数，将JSON格式的字节切片转换为 `Person` 结构体。如果转换过程中发生错误，会打印错误信息并返回。最后，输出 `Person` 结构体的字段值。

需要注意的是，在使用 `json.Unmarshal` 函数时，需要传入一个指向结构体的指针作为第二个参数，用于接收转换后的结构体数据。

### （四）处理JSON数组

在Go语言中，可以使用切片来表示JSON数组。通过使用 `json.Unmarshal` 函数，可以将JSON格式的字节切片转换为Go切片。

下面是一个示例代码：

```
package main

import (
    "encoding/json"
    "fmt"
)

// 定义一个结构体
type Person struct {
    Name    string   `json:"name"`
    Age     int      `json:"age"`
    Hobbies []string `json:"hobbies"`
}

func main() {
    // JSON数组数据
    jsonData := `[{"name":"John Doe","age":30,"hobbies":["reading","writing","coding"]},{"name":"Jane Doe","age":25,"hobbies":["drawing","painting"]}]`

    // 将JSON格式的字节切片转换为Person结构体切片
    var people []Person
    err := json.Unmarshal([]byte(jsonData), &people)
    if err!= nil {
        fmt.Println("Error:", err)
        return
    }

    // 输出Person结构体切片的字段值
    for _, person := range people {
        fmt.Println("Name:", person.Name)
        fmt.Println("Age:", person.Age)
        fmt.Println("Hobbies:", person.Hobbies)
        fmt.Println("---")
    }
}
```

在上述代码中，定义了一个 `Person` 结构体，包含 `Name`、`Age` 和 `Hobbies` 三个字段。通过使用 `json.Unmarshal` 函数，将JSON格式的字节切片转换为 `Person` 结构体切片。如果转换过程中发生错误，会打印错误信息并返回。最后，遍历 `Person` 结构体切片，输出每个结构体的字段值。

### （五）格式化JSON输出

在Go语言中，可以使用 `json.MarshalIndent` 函数将Go结构体转换为格式化的JSON格式的字节切片。

下面是一个示例代码：

```
package main

import (
    "encoding/json"
    "fmt"
)

// 定义一个结构体
type Person struct {
    Name    string   `json:"name"`
    Age     int      `json:"age"`
    Hobbies []string `json:"hobbies"`
}

func main() {
    // 创建一个Person结构体实例
    person := Person{
        Name:    "John Doe",
        Age:     30,
        Hobbies: []string{"reading", "writing", "coding"},
    }

    // 将Person结构体转换为格式化的JSON格式的字节切片
    jsonData, err := json.MarshalIndent(person, "", "  ")
    if err!= nil {
        fmt.Println("Error:", err)
        return
    }

    // 输出格式化的JSON数据
    fmt.Println(string(jsonData))
}
```

在上述代码中，定义了一个 `Person` 结构体，包含 `Name`、`Age` 和 `Hobbies` 三个字段。通过使用 `json.MarshalIndent` 函数，将 `Person` 结构体转换为格式化的JSON格式的字节切片。其中，第一个参数是要转换的结构体，第二个参数是缩进的前缀，第三个参数是缩进的宽度。如果转换过程中发生错误，会打印错误信息并返回。最后，将格式化的JSON格式的字节切片转换为字符串并输出。
