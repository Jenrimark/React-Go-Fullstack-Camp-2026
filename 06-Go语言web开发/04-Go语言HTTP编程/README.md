# Go语言HTTP编程

来源：
- https://campus.wps.cn/contentpreview/a7603ccb-eb01-4bff-91a2-07e7d43231aa

# Go语言http编程

# Go 语言的 net/http 包

Go 语言的`net/http`包提供了 HTTP 客户端和服务器的实现。

[http标准库](https://pkg.go.dev/net/http@go1.25.3)

# 构建 HTTP 服务器

## 创建简单的 HTTP 服务器

```
package main

import (
    "fmt"
    "net/http"
)

func main() {
    // 处理/hello路径的请求
    http.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "你好，Gopher！")
    })

    // 启动服务器在8080端口
    fmt.Println("服务器启动在 :8080...")
    http.ListenAndServe(":8080", nil)
}
```

## 实践练习：文件服务器

创建一个简单的文件服务器：

```
package main

import (
    "log"
    "net/http"
)

func main() {
    // 将当前目录作为静态文件服务器的根目录
    fileServer := http.FileServer(http.Dir("."))

    // 注册处理器
    http.Handle("/", fileServer)

    log.Println("文件服务器启动在 :8080...")
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```

> 注意：go 语言的 net/http 包只是提供了基础的能力，如果需要更复杂的 http 功能，可以考虑使用第三方库，比如：
>
> - [Gin](https://github.com/gin-gonic/gin)
> - [Echo](https://github.com/labstack/echo)
> - [Beego](https://github.com/astaxie/beego)

# HTTP 客户端编程

go 语言的 http 客户端编程，主要使用 http.Client 结构体，用来发送 http 请求，并获取响应。  
注意：一个服务端程序，可以同时作为客户端，发送 http 请求。

## 发送 GET 请求

```
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    // 发送GET请求
    resp, err := http.Get("http://example.com")
    if err != nil {
        fmt.Println("请求失败:", err)
        return
    }
    defer resp.Body.Close()

    // 读取响应内容
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Println("读取响应失败:", err)
        return
    }

    fmt.Printf("状态码: %d\n", resp.StatusCode)
    fmt.Printf("响应内容: %s\n", body)
}
```

## 发送 POST 请求

```
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "net/http"
)

func main() {
    // 准备要发送的数据
    data := map[string]string{
        "name": "小明",
        "age":  "18",
    }

    jsonData, err := json.Marshal(data)
    if err != nil {
        fmt.Println("JSON编码失败:", err)
        return
    }

    // 发送POST请求
    resp, err := http.Post("http://example.com/api",
        "application/json",
        bytes.NewBuffer(jsonData))
    if err != nil {
        fmt.Println("请求失败:", err)
        return
    }
    defer resp.Body.Close()

    fmt.Printf("状态码: %d\n", resp.StatusCode)
}
```

## 实践练习：使用 http.Client 发送请求

访问 <https://jsonplaceholder.typicode.com/> 查看接口列表

```
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
)

type TodoResponse struct {
	Title     string `json:"title"`
	UserId    int    `json:"userId"`
	Id        int    `json:"id"`
	Completed bool   `json:"completed"`
}

func main() {
	// 注意：这是一个示例URL，实际使用时需要替换为真实的天气API
	resp, err := http.Get("https://jsonplaceholder.typicode.com/todos/1")
	if err != nil {
		fmt.Println("获取todo信息失败:", err)
		return
	}
	defer resp.Body.Close()

	var todo TodoResponse
	if err := json.NewDecoder(resp.Body).Decode(&todo); err != nil {
		fmt.Println("解析todo信息失败:", err)
		return
	}

	fmt.Printf("title: %s\nuserId: %d\nid: %d\ncompleted: %t\n",
		todo.Title, todo.UserId, todo.Id, todo.Completed)
}
```

### 课堂练习一：

目录： week03/practice/http-1/main.go

1. 使用 http.Client 发送请求，获取 <https://jsonplaceholder.typicode.com/todos> 列表
2. 打印出每个 todo 的 title, userId, id, completed

### 课堂练习二：

目录： week03/practice/http-2//main.go

1. 本地启动一个 http 服务，监听 8080 端口
2. 访问 /api/todo/detail/:todoid 返回对应 id 的 todo 的 title, userId, id, completed
3. 访问 /api/todo/list 返回所有 todo 列表

其他教程：  
[Go 语言 net/http 模块使用教程](https://go.sixue.work/golang/c05/c05_05.html)
