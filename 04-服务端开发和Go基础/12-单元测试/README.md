# 单元测试

来源：
- https://campus.wps.cn/contentpreview/be5a6c81-c36e-4151-8d4e-679d54423840

# Go语言单元测试

# Go 单元测试详细教程

## 1. 基础要求

- 测试文件必须以 `_test.go` 结尾
- 测试函数格式：`func TestXxx(*testing.T)`
- 需要 `import "testing"` 包
- 使用 `go test` 命令执行测试

## 2. 快速入门示例

### 被测代码 `math.go`：

```
package math

func Add(a, b int) int {
    return a + b
}

func Divide(a, b int) (int, error) {
    if b == 0 {
        return 0, fmt.Errorf("cannot divide by zero")
    }
    return a / b, nil
}
```

### 测试代码 `math_test.go`：

```
package math

import (
    "testing"
)

func TestAdd(t *testing.T) {
    result := Add(2, 3)
    if result != 5 {
        t.Errorf("Expected 5, got %d", result)
    }
}

func TestDivide(t *testing.T) {
    t.Run("normal division", func(t *testing.T) {
        result, err := Divide(6, 2)
        if err != nil {
            t.Fatal("unexpected error:", err)
        }
        if result != 3 {
            t.Error("Expected 3, got", result)
        }
    })

    t.Run("divide by zero", func(t *testing.T) {
        _, err := Divide(5, 0)
        if err == nil {
            t.Fatal("expected error but got nil")
        }
    })
}
```

## 3. 核心测试方法

### 常用方法：

- `t.Error()` / `t.Errorf()`：标记测试失败，继续执行
- `t.Fatal()` / `t.Fatalf()`：标记测试失败，立即终止
- `t.Log()` / `t.Logf()`：输出日志（仅在 `-v` 参数时显示）
- `t.Run()`：创建子测试

### 测试命令：

```
# 运行当前目录所有测试
go test -v

# 运行指定测试函数
go test -v -run TestAdd

# 查看测试覆盖率
go test -cover
go test -coverprofile=coverage.out
go tool cover -html=coverage.out
```

## 4. Table-Driven Tests（表驱动测试详解）

表驱动测试是 Go 语言中最推荐的测试方法之一。它通过构建测试用例表来实现多场景测试，具有以下优点：

- 测试用例清晰明了
- 易于添加新的测试用例
- 代码复用性高
- 维护成本低

### 4.1 基本结构

```
func TestXxx(t *testing.T) {
    // 1. 定义测试用例结构
    tests := []struct {
        name     string    // 测试用例名称
        input    string    // 输入参数
        expected string    // 期望结果
        wantErr  bool     // 是否期望错误
    }{
        // 2. 编写测试用例
        {
            name:     "case1",
            input:    "hello",
            expected: "HELLO",
            wantErr:  false,
        },
        // 更多测试用例...
    }

    // 3. 执行测试用例
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            // 4. 执行被测试的函数
            actual, err := YourFunction(tt.input)

            // 5. 检查错误
            if (err != nil) != tt.wantErr {
                t.Errorf("期望错误: %v, 实际得到错误: %v", tt.wantErr, err)
            }

            // 6. 检查结果
            if actual != tt.expected {
                t.Errorf("期望: %v, 实际: %v", tt.expected, actual)
            }
        })
    }
}
```

### 4.2 实际示例：字符串处理函数测试

```
// 被测试的函数
func ProcessString(s string) (string, error) {
    if s == "" {
        return "", fmt.Errorf("空字符串")
    }
    return strings.ToUpper(s), nil
}

// 测试代码
func TestProcessString(t *testing.T) {
    tests := []struct {
        name     string
        input    string
        expected string
        wantErr  bool
    }{
        {
            name:     "正常字符串",
            input:    "hello",
            expected: "HELLO",
            wantErr:  false,
        },
        {
            name:     "空字符串",
            input:    "",
            expected: "",
            wantErr:  true,
        },
        {
            name:     "中文字符串",
            input:    "你好",
            expected: "你好",
            wantErr:  false,
        },
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := ProcessString(tt.input)
            if (err != nil) != tt.wantErr {
                t.Errorf("ProcessString() error = %v, wantErr %v", err, tt.wantErr)
                return
            }
            if got != tt.expected {
                t.Errorf("ProcessString() = %v, want %v", got, tt.expected)
            }
        })
    }
}
```

### 4.3 练习：编写表驱动测试

请为以下函数编写表驱动测试：

```
// 计算两个数的最大公约数
func GCD(a, b int) int {
    for b != 0 {
        a, b = b, a%b
    }
    return a
}
```

## 5. 高级测试技巧

### 5.1 使用 testify 断言库

```
import (
    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/require"
)

func TestAddWithAssert(t *testing.T) {
    result := Add(2, 3)
    assert.Equal(t, 5, result, "should equal 5")

    _, err := Divide(5, 0)
    require.Error(t, err, "should return error")
}
```

### 5.2 基准测试

```
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(1, 2)
    }
}
```

### 5.3 模拟时间

```
func TestTimeSensitive(t *testing.T) {
    fakeNow := time.Date(2023, 1, 1, 0, 0, 0, 0, time.UTC)
    time.Now = func() time.Time { return fakeNow }
    defer func() { time.Now = time.Now }()

    // 测试代码...
}
```

## 6. 测试组织建议

1. **目录结构**：

   ```
   /pkg
     /math
       math.go
       math_test.go
   ```
2. **测试类型**：

   - 单元测试：`xxx_test.go`
   - 集成测试：`xxx_integration_test.go`
   - 性能测试：`xxx_benchmark_test.go`
3. **测试覆盖率**：

   - 推荐目标 70% 以上核心逻辑
   - 使用 `-cover` 参数查看覆盖率

## 7. 常见陷阱

1. **循环依赖**：

   - 使用 `package math_test` 避免测试包循环依赖
   - 通过导出接口进行测试
2. **并发测试**：

   ```
   func TestConcurrent(t *testing.T) {
       t.Parallel() // 标记为可并行
       // 测试代码...
   }
   ```
3. **测试初始化**：

   ```
   func TestMain(m *testing.M) {
       setup()
       code := m.Run()
       teardown()
       os.Exit(code)
   }
   ```

## 8. Mock 测试（使用 gomock）

1. 安装 mockgen：

```
go install go.uber.org/mock/mockgen@latest
```

2. 生成 mock 代码：

```
mockgen -source=repository.go -destination=repository_mock.go -package=mocks
```

3. 使用 mock：

```
func TestService(t *testing.T) {
    ctrl := gomock.NewController(t)
    defer ctrl.Finish()

    mockRepo := mocks.NewMockRepository(ctrl)
    mockRepo.EXPECT().GetData().Return("mocked data", nil)

    service := NewService(mockRepo)
    // 测试代码...
}
```

## 当我在 basic 目录执行 go test 的时候，会依次执行哪些代码？

当你在 basic 目录执行 `go test` 命令时，Go 会按照以下顺序执行代码：

1. **测试文件查找**

   - Go 会查找所有以 `_test.go` 结尾的文件
   - 这些文件可以包含以下类型的测试：
     - 单元测试（以 `Test` 开头的函数）
     - 基准测试（以 `Benchmark` 开头的函数）
     - 示例测试（以 `Example` 开头的函数）
2. **执行顺序**

```
// 1. 首先执行 TestMain (如果存在)
func TestMain(m *testing.M) {
    // 测试设置代码
    code := m.Run() // 运行实际测试
    // 测试清理代码
    os.Exit(code)
}

// 2. 执行 init() 函数 (如果存在)
func init() {
    // 初始化代码
}

// 3. 按照字母顺序执行测试函数
func TestA(t *testing.T) { ... }
func TestB(t *testing.T) { ... }
func TestC(t *testing.T) { ... }
```

3. **具体执行流程**

   - 对于每个测试函数：
     1. 设置测试环境
     2. 执行测试代码
     3. 清理测试环境
     4. 报告测试结果
4. **特殊情况**

   - 如果使用 `-parallel` 标志，多个测试可能并行执行
   - 使用 `-run` 标志可以只运行特定的测试
   - 使用 `-v` 标志可以看到详细的测试执行过程

示例命令：

```
go test                     # 运行所有测试
go test -v                  # 运行所有测试并显示详细信息
go test -run TestXxx       # 只运行名称匹配 TestXxx 的测试
go test -parallel 4        # 最多允许 4 个测试并行执行
```

5. **测试覆盖率**
   - 如果使用 `-cover` 标志，Go 还会收集代码覆盖率信息：

```
go test -cover            # 显示测试覆盖率
go test -coverprofile=coverage.out  # 生成覆盖率报告文件
```

记住：

- 测试文件必须以 `_test.go` 结尾
- 测试函数必须以 `Test`、`Benchmark` 或 `Example` 开头
- 每个包的测试都是独立的
- 测试文件可以访问被测试包中的所有内部函数和变量

# 其他教程

<https://golang.iswbm.com/c08/c08_01.html#>  
<https://www.topgoer.com/%E5%87%BD%E6%95%B0/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95.html>

## 8. 性能测试详解

性能测试（Benchmark）用于测试代码的性能和资源消耗。Go 提供了强大的性能测试支持。

### 8.1 基本结构

```
func BenchmarkXxx(b *testing.B) {
    // 可选：重置计时器
    b.ResetTimer()

    // b.N 是基准测试框架提供的循环次数
    for i := 0; i < b.N; i++ {
        // 被测试的代码
    }
}
```

### 8.2 完整示例

```
// 被测试的函数
func Fibonacci(n int) int {
    if n < 2 {
        return n
    }
    return Fibonacci(n-1) + Fibonacci(n-2)
}

// 基准测试
func BenchmarkFibonacci(b *testing.B) {
    // 测试不同输入规模
    for n := 0; n < b.N; n++ {
        Fibonacci(10)
    }
}

// 并行基准测试
func BenchmarkFibonacciParallel(b *testing.B) {
    // 使用并行测试
    b.RunParallel(func(pb *testing.PB) {
        for pb.Next() {
            Fibonacci(10)
        }
    })
}
```

### 8.3 性能测试命令

```
# 运行基准测试
go test -bench=.

# 运行特定的基准测试
go test -bench=BenchmarkFibonacci

# 显示内存分配统计
go test -bench=. -benchmem

# 运行时间更长的基准测试
go test -bench=. -benchtime=5s

# 指定 CPU 核心数
go test -bench=. -cpu=1,2,4
```

### 8.4 性能测试结果解读

```
BenchmarkFibonacci-8           1000000              1234 ns/op
```

- `BenchmarkFibonacci-8`: 测试名称，-8 表示使用 8 个 CPU
- `1000000`: 测试执行的次数
- `1234 ns/op`: 每次操作平均耗时（纳秒）

### 8.5 内存分配统计

```
BenchmarkXxx-8    1000000    1234 ns/op    123 B/op    4 allocs/op
```

- `123 B/op`: 每次操作分配的内存字节数
- `4 allocs/op`: 每次操作的内存分配次数

### 8.6 练习：编写性能测试

为以下函数编写性能测试：

```
// 计算切片的和
func Sum(numbers []int) int {
    total := 0
    for _, n := range numbers {
        total += n
    }
    return total
}
```

提示：

1. 准备不同大小的测试数据
2. 测试不同的数据规模
3. 比较并行和串行性能

# 总结

通过本节课程的学习，您已经掌握了：

1. Go 语言单元测试的基础知识

   - 测试文件的命名规则
   - 测试函数的格式
   - 基本的测试命令
2. 进阶测试技巧

   - 表驱动测试的编写方法
   - 性能测试的实现
   - Mock 测试的使用
3. 最佳实践

   - 测试代码的组织方式
   - 测试覆盖率的控制
   - 常见陷阱的避免

建议：

- 在实际项目中，应该保持测试代码的质量与生产代码同等重要
- 根据具体需求选择合适的测试方法
- 持续关注测试覆盖率，确保核心逻辑的测试覆盖
- 多进行练习，熟练掌握各种测试技巧

# 其他教程

<https://golang.iswbm.com/c08/c08_01.html#>  
<https://www.topgoer.com/%E5%87%BD%E6%95%B0/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95.html>
