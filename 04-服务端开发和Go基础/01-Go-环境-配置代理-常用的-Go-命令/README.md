# Go 环境，配置代理，常用的 Go 命令

来源：
- https://campus.wps.cn/contentpreview/4614f63c-99ca-46bd-aec0-e839f3641104

# Go语言环境安装

# 1.1 Go 语言的历史和设计理念

Go 语言（也称为 Golang）是由 Google 公司在 2007 年开始设计，并于 2009 年正式对外发布的一种静态强类型、编译型、并发型编程语言。该语言由 Robert Griesemer、Rob Pike 和 Ken Thompson 主持开发，三位均是计算机科学领域的重要人物。

## 1.1.1 Go 语言的起源

Go 语言的诞生源于 Google 内部对现有编程语言的不满：

- C/C++ 编译速度慢，语法复杂
- Java 和 C# 代码冗长，类型系统过于复杂
- Python 和其他动态语言执行效率低，且缺乏静态类型检查

Go 语言旨在结合静态类型语言的安全性和动态语言的易用性，同时提供现代化的并发编程支持。

## 1.1.2 设计理念

Go 语言的核心设计理念包括：

1. **简洁**：Go 语言的语法简单明了，关键字只有 25 个，易于学习和使用。
2. **高效**：Go 编译速度快，执行效率高，接近 C/C++。
3. **安全**：静态类型系统有助于在编译时捕获错误。
4. **并发**：内置并发支持（goroutines 和 channels），使并发编程变得简单。
5. **垃圾回收**：自动内存管理，减轻开发者的负担。
6. **跨平台**：支持交叉编译，可以轻松地为不同操作系统生成可执行文件。

Go 语言被设计用来解决大型软件工程问题，特别适合于网络服务、系统编程和云计算等领域。

# 1.2 安装 Go 环境

## 1.2.1 下载与安装

根据不同的操作系统，安装方式略有不同：

**Windows 系统**：

1. 访问 [Go 官方下载页面](https://golang.org/dl/)
2. 下载 Windows 安装包（.msi 文件）
3. 双击安装包，按照提示完成安装

**macOS 系统**：

1. 使用 Homebrew 安装：`brew install go`
2. 或访问官方下载页面，下载 macOS 安装包（.pkg 文件）并安装

**Linux 系统**：

1. 访问官方下载页面，下载 Linux 压缩包（.tar.gz 文件）
2. 解压到 /usr/local 目录：`sudo tar -C /usr/local -xzf go1.x.x.linux-amd64.tar.gz`
3. 添加环境变量（在 ~/.profile 或 ~/.bashrc 文件中）：`export PATH=$PATH:/usr/local/go/bin`

安装完成后，打开终端（命令行），输入 `go version`，如果显示 Go 的版本信息，则表示安装成功。

## 1.2.2 配置 GOPATH 和 GOROOT

**GOROOT**：Go 语言的安装路径，安装时通常会自动设置。

**GOPATH**：Go 项目的工作目录，用于存放源代码、依赖包和编译后的文件。

在 Go 1.8 版本之前，必须手动设置 GOPATH；从 Go 1.8 开始，如果没有设置 GOPATH，则使用默认值：

- Windows：`%USERPROFILE%\go`
- Unix/Linux/macOS：`$HOME/go`

推荐的 GOPATH 目录结构：

- `src/`：源代码目录
- `pkg/`：包对象目录
- `bin/`：可执行文件目录

**设置环境变量**：

Windows：

```
控制面板 -> 系统和安全 -> 系统 -> 高级系统设置 -> 环境变量
添加 GOPATH 变量，值为你的工作目录路径
```

macOS/Linux：

```
# 编辑 ~/.bashrc 或 ~/.zshrc 文件
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```

> **小练习 1**：
>
> 1. 安装 Go 语言环境
> 2. 验证安装是否成功（使用 `go version` 命令）
> 3. 查看并记录你的 GOPATH 路径（使用 `go env GOPATH` 命令）

# 1.3 Go 命令行工具介绍

## 1.3.1 常用命令

| 命令 | 说明 | 示例 |
| --- | --- | --- |
| `go build` | 编译 Go 程序 | `go build hello.go` |
| `go run` | 编译并运行 Go 程序 | `go run hello.go` |
| `go fmt` | 格式化 Go 代码 | `go fmt ./...` |
| `go get` | 下载并安装包和依赖 | `go get github.com/user/package` |
| `go install` | 编译并安装包 | `go install github.com/user/package` |
| `go test` | 运行测试 | `go test ./...` |
| `go mod` | 模块管理 | `go mod init myproject` |
| `go version` | 显示 Go 版本 | `go version` |
| `go env` | 显示 Go 环境信息 | `go env` |

## 1.3.2 代码格式化

Go 语言强调代码风格的一致性，`go fmt` 命令会自动调整代码格式以符合官方规范，包括：

- 使用制表符（tab）而非空格缩进
- 行长度管理
- 括号位置规范
- 等号对齐

# 1.4 第一个 Go 程序（Hello World）

## 1.4.1 创建并运行 Hello World

1. 创建一个新目录：

   ```
   mkdir hello
   cd hello
   ```
2. 初始化模块：

   ```
   go mod init hello
   ```
3. 创建文件 `hello.go`，内容如下：

   ```
   package main // 声明包名

   import "fmt" // 导入格式化输出包

   // main 函数是程序执行的入口
   func main() {
       // 打印 Hello, Go! 到控制台
       fmt.Println("Hello, Go!")
   }
   ```
4. 运行程序：

   ```
   go run hello.go
   ```
5. 编译为可执行文件：

   ```
   go build
   # Windows系统会生成 hello.exe，Unix/Linux系统会生成 hello
   ```

**step by step 小练习**：

> git clone 你的代码 并进入目录，使用vscode打开。  
> cd week01/practice/hello 创建一个main.go文件
>
> 1. 修改 Hello World 程序，使其打印你的名字
> 2. 尝试添加多行注释和单行注释
> 3. 使用 `go build` 命令编译程序并运行生成的可执行文件
> 4. 使用 `go run main.go` 命令运行程序
> 5. 创建 .gitignore 文件，忽略生成的可执行文件和test目录
> 6. 使用 `git add .` 命令添加文件到暂存区
> 7. 使用 `git commit -m "add hello world program"` 命令提交文件到本地仓库
> 8. 使用 `git push origin master` 命令提交文件到远程仓库
> 9. 查看你的git网页，查看你的代码是否提交成功

## 1.4.2 代码详解

让我们逐行分析这个简单的程序：

- `package main`：每个 Go 程序都必须属于一个包。`main` 包是特殊的，它定义了一个独立的可执行程序，而不是库。
- `import "fmt"`：导入标准库中的 `fmt` 包，用于格式化输入输出。
- `func main() { ... }`：`main` 函数是程序的入口点。当运行可执行程序时，会从 `main` 函数开始执行。main 函数必须在 main 包中。
- `fmt.Println("Hello, Go!")`：调用 `fmt` 包中的 `Println` 函数打印文本到控制台。

# 1.5 Go 语言 Roadmap

[Go 语言 Roadmap](https://roadmap.sh/golang)

[Go 语言 初中高级学习路线](https://codechina.gitcode.host/developer-roadmap/go/intro/junior/)

# 1.6 本章小结

在本章中，我们：

- 了解了 Go 语言的历史和设计理念
- 安装并配置了 Go 语言环境
- 学习了基本的 Go 命令行工具
- 创建并运行了第一个 Go 程序
