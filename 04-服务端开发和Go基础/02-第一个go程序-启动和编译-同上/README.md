# 第一个go程序，启动和编译（同上）

来源：
- https://campus.wps.cn/contentpreview/4614f63c-99ca-46bd-aec0-e839f3641104

说明：
- 该目录在总目录中标注“同上”，正文从共享页面中按对应小节拆出。

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
