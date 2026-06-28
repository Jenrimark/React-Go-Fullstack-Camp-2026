# 互斥锁和读写锁

来源：
- https://campus.wps.cn/contentpreview/5f6bf8a6-15b2-4743-bf96-edd78b02139a

# Go语言高并发进阶

# 互斥锁和读写锁

在并发编程中，多个goroutine同时访问共享资源可能会导致数据竞争。Go语言提供了sync包中的Mutex和RWMutex来解决这个问题。  
什么是数据竞争？以下是一个例子：

```
package main

import (
    "fmt"
    "sync"
)

func main() {
  var count int
  var wg sync.WaitGroup

  for i := 0; i < 1000; i++ {
    wg.Add(1)
    go func() {
      defer wg.Done()
      count++
    }()
  }
  wg.Wait()
  fmt.Println(count)
}
```

上面的代码，1000个goroutine同时对count进行++操作，结果是1000，而不是1。这是因为count++操作不是原子操作，而是由三步组成：

1. 读取count的值
2. 将count的值加1
3. 将count的值写回内存

在多goroutine同时执行时，可能会出现以下情况：

```
goroutine 1: 读取 count = 0
goroutine 2: 读取 count = 0
goroutine 1: 将 count 加 1，得到 1
goroutine 2: 将 count 加 1，得到 1
goroutine 1: 将 count 写回内存，count = 1
```

## sync.Mutex的基本用法

Mutex是最基本的互斥锁，用于保护共享资源。当一个goroutine获得了锁，其他goroutine必须等待锁释放才能访问被保护的资源。

```
package main

import (
    "fmt"
    "sync"
)

type Counter struct {
    mu    sync.Mutex
    count int
}

func (c *Counter) Increment() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.count++
}

func (c *Counter) GetCount() int {
    c.mu.Lock()
    defer c.mu.Unlock()
    return c.count
}

func main() {
    counter := Counter{}
    var wg sync.WaitGroup

    // 启动100个goroutine同时增加计数
    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            counter.Increment()
        }()
    }

    wg.Wait()
    fmt.Printf("最终计数: %d\n", counter.GetCount())
}
```

## sync.RWMutex的基本用法

RWMutex是读写锁，它允许多个读操作同时进行，但写操作是互斥的。这在读多写少的场景下性能更好。

以一个map为例，使用RWMutex来保护map的读写操作。

```
package main

import (
    "fmt"
    "sync"
)

type SafeMap struct {
    rwmu sync.RWMutex
    data map[string]int
}

func NewSafeMap() *SafeMap {
    return &SafeMap{
        data: make(map[string]int),
    }
}

func (sm *SafeMap) Set(key string, value int) {
    sm.rwmu.Lock()
    defer sm.rwmu.Unlock()
    sm.data[key] = value
}

func (sm *SafeMap) Get(key string) (int, bool) {
    sm.rwmu.RLock()
    defer sm.rwmu.RUnlock()
    value, exists := sm.data[key]
    return value, exists
}

func main() {
    sm := NewSafeMap()
    var wg sync.WaitGroup

    // 写入数据
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func(n int) {
            defer wg.Done()
            sm.Set(fmt.Sprintf("key%d", n), n)
        }(i)
    }

    // 读取数据
    for i := 0; i < 100; i++ {
        wg.Add(1)
        go func(n int) {
            defer wg.Done()
            key := fmt.Sprintf("key%d", n%10)
            if value, exists := sm.Get(key); exists {
                fmt.Printf("读取 %s: %d\n", key, value)
            }
        }(i)
    }

    wg.Wait()
}
```

## Mutex和RWMutex的区别与选择

1. **锁的类型和用途**

   - Mutex（互斥锁）：是一个简单的互斥锁，同一时刻只允许一个goroutine访问共享资源
   - RWMutex（读写锁）：分为读锁和写锁两种，支持多个读操作并发执行，但写操作是互斥的
2. **性能特点**

   - Mutex：所有操作都是互斥的，无论是读还是写
   - RWMutex：读操作可以并发执行，写操作需要等待所有读操作完成
3. **适用场景**

   - Mutex：适合读写频率相近的场景
   - RWMutex：适合读多写少的场景，能显著提升性能
4. **加锁方法**

   - Mutex：只有`Lock()`和`Unlock()`两个方法
   - RWMutex：有`RLock()`、`RUnlock()`（读锁）和`Lock()`、`Unlock()`（写锁）四个方法

让我们通过一个例子来对比它们的使用和性能差异：

```
package main

import (
    "fmt"
    "sync"
    "time"
)

// 使用Mutex的计数器
type MutexCounter struct {
    mu    sync.Mutex
    count int
}

// 使用RWMutex的计数器
type RWMutexCounter struct {
    mu    sync.RWMutex
    count int
}

func main() {
    // 创建两种计数器
    mc := &MutexCounter{}
    rwc := &RWMutexCounter{}
    
    // 测试Mutex
    start := time.Now()
    var wg sync.WaitGroup
    
    // 启动100个goroutine进行读操作
    for i := 0; i < 100; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            // 模拟读操作
            mc.mu.Lock()
            _ = mc.count
            mc.mu.Unlock()
            time.Sleep(time.Millisecond)
        }()
    }
    
    // 启动10个goroutine进行写操作
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            mc.mu.Lock()
            mc.count++
            mc.mu.Unlock()
            time.Sleep(time.Millisecond)
        }()
    }
    
    wg.Wait()
    fmt.Printf("Mutex耗时: %v\n", time.Since(start))
    
    // 测试RWMutex
    start = time.Now()
    
    // 启动100个goroutine进行读操作
    for i := 0; i < 100; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            // 使用读锁
            rwc.mu.RLock()
            _ = rwc.count
            rwc.mu.RUnlock()
            time.Sleep(time.Millisecond)
        }()
    }
    
    // 启动10个goroutine进行写操作
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            rwc.mu.Lock()
            rwc.count++
            rwc.mu.Unlock()
            time.Sleep(time.Millisecond)
        }()
    }
    
    wg.Wait()
    fmt.Printf("RWMutex耗时: %v\n", time.Since(start))
}
```

5. **使用注意事项**

   - Mutex使用注意事项：

     - 不要在一个goroutine中重复加锁
     - 要确保锁被正确释放，推荐使用defer
     - 避免在持有锁时进行耗时操作
   - RWMutex使用注意事项：

     - 写锁比读锁优先级高
     - 写锁会阻塞其他写锁和读锁
     - 读锁只会阻塞写锁，不会阻塞其他读锁
     - 使用RWMutex时，如果读操作很快，锁竞争不大，使用Mutex可能更简单且足够
6. **性能对比**

| 特性 | Mutex | RWMutex |
| --- | --- | --- |
| 并发读 | 不支持 | 支持 |
| 写操作 | 互斥 | 互斥 |
| 实现复杂度 | 简单 | 较复杂 |
| 适用场景 | 读写频率接近 | 读多写少 |
| 内存开销 | 较小 | 较大 |
| 性能 | 读写都互斥，竞争大时性能下降 | 读操作并发，读多时性能好 |

# 原子操作

原子操作：在计算机科学中，原子操作是指不可中断的操作，要么完全执行，要么完全不执行，不存在中间状态。对于多线程或多 goroutine 的程序环境，原子操作至关重要，因为它能确保在并发访问共享资源时数据的一致性和完整性。

## 为什么需要原子操作？

代码中的加锁操作因为涉及内核态的上下文切换会比较耗时、代价比较高。针对基本数据类型我们还可以使用原子操作来保证并发安全，因为原子操作是Go语言提供的方法它在用户态就可以完成，因此性能比加锁操作更好。Go语言中原子操作由内置的标准库sync/atomic提供。

## atomic包的主要操作

1. **原子加法**

```
package main

import (
    "fmt"
    "sync"
    "sync/atomic"
)

func main() {
    var count int64
    var wg sync.WaitGroup

    // 启动1000个goroutine进行原子加操作
    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go func() {
            atomic.AddInt64(&count, 1) // 原子地将count加1
            wg.Done()
        }()
    }

    wg.Wait()
    fmt.Printf("使用原子操作后的结果：%d\n", count) // 一定是1000
}
```

2. **原子存储和加载**

原子存储和加载是原子操作的另外两个重要操作。原子存储是将一个值原子地存储到内存中，原子加载是从内存中原子地加载一个值。主要用于读写共享变量。

```
func main() {
    var value int64

    // 原子地存储值
    atomic.StoreInt64(&value, 100)

    // 原子地加载值
    v := atomic.LoadInt64(&value)
    fmt.Printf("加载的值：%d\n", v)
}
```

LoadInt64和StoreInt64是原子操作的另外两个重要操作。LoadInt64是从内存中原子地加载一个值，StoreInt64是将一个值原子地存储到内存中。和直接读取&value相比，原子操作可以保证操作的原子性，避免出现数据竞争。

> 可以了解一下不使用LoadInt64而使用直接读取&value的后果。

## 实际应用场景

1. **计数器**  
   适用于需要在多个goroutine间共享的计数器，如：

- 网站访问量统计
- 并发任务完成数量统计
- 资源使用计数

```
type Counter struct {
    value int64
}

func (c *Counter) Increment() {
    atomic.AddInt64(&c.value, 1)
}

func (c *Counter) GetValue() int64 {
    return atomic.LoadInt64(&c.value)
}
```

## 使用建议

1. 当只需要对基本类型（整数、指针等）进行简单操作时，优先使用原子操作而不是互斥锁
2. 当需要保护复杂的数据结构或多个操作时，使用互斥锁
3. 不要把原子操作和普通操作混用，这可能导致数据竞争
4. 记住原子操作的局限性，它只能用于基本的数值类型

# sync.Once

## 为什么需要sync.Once？

在并发编程中，我们经常需要确保某些操作只执行一次，例如：

- 初始化配置
- 建立数据库连接
- 加载资源文件
- 创建单例对象

sync.Once就是为了解决这个问题而设计的。它能保证在多个goroutine并发的情况下，某个操作只会被执行一次。

## sync.Once的基本用法

sync.Once只有一个方法：Do(f func())。这个方法接收一个无参数的函数作为参数，并确保这个函数只会被执行一次。

```
package main

import (
    "fmt"
    "sync"
)

type Config struct {
    Settings map[string]string
}

var (
    config *Config
    once   sync.Once
)

func GetConfig() *Config {
    once.Do(func() {
        fmt.Println("初始化配置...")
        config = &Config{
            Settings: make(map[string]string),
        }
        config.Settings["host"] = "localhost"
        config.Settings["port"] = "8080"
    })
    return config
}

func main() {
    var wg sync.WaitGroup
    
    // 并发获取配置
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            c := GetConfig()
            fmt.Printf("获取配置: %+v\n", c.Settings)
        }()
    }
    
    wg.Wait()
}
```

## sync.Once的特点

1. **只执行一次**：无论Do方法被调用多少次，传入的函数只会被执行一次
2. **线程安全**：可以在多个goroutine中安全使用
3. **延迟初始化**：直到第一次调用Do方法时才会执行初始化
4. **不可重置**：一旦执行完成，就不能重置为未执行状态

## 使用场景

1. **单例模式**

```
type Singleton struct {
    data string
}

var (
    instance *Singleton
    once     sync.Once
)

func GetInstance() *Singleton {
    once.Do(func() {
        instance = &Singleton{data: "初始化数据"}
    })
    return instance
}
```

2. **资源初始化**

```
type Resource struct {
    cache map[string]string
}

var (
    resource *Resource
    initOnce sync.Once
)

func InitResource() {
    initOnce.Do(func() {
        resource = &Resource{
            cache: make(map[string]string),
        }
        // 加载资源...
    })
}
```

# sync.Map

## 为什么需要sync.Map？

Go语言的内置map不是并发安全的。在多个goroutine同时读写map时，会导致panic。传统的解决方案是使用互斥锁保护map，但这种方式在某些场景下性能不够理想。sync.Map是Go语言提供的一个并发安全的map实现。

例如内置map的一个例子：

```
package main

import (
    "fmt"
    "strconv"
    "sync"
)

var m = make(map[string]int)

func get(key string) int {
    return m[key]
}

func set(key string, value int) {
    m[key] = value
}

func main() {
    wg := sync.WaitGroup{}
    for i := 0; i < 20; i++ {
        wg.Add(1)
        go func(n int) {
            key := strconv.Itoa(n)
            set(key, n)
            fmt.Printf("k=:%v,v:=%v\n", key, get(key))
            wg.Done()
        }(i)
    }
    wg.Wait()
}
```

## sync.Map相关方法

sync.Map 提供了以下主要方法：

1. **Store(key, value interface{})**

   - 存储键值对
   - 如果key已存在，则更新value
2. **Load(key interface{}) (value interface{}, ok bool)**

   - 获取key对应的value
   - 如果key不存在，返回nil和false
   - 如果key存在，返回对应的value和true
3. **Delete(key interface{})**

   - 删除指定的key及其对应的value
   - 如果key不存在，不会报错
4. **LoadOrStore(key, value interface{}) (actual interface{}, loaded bool)**

   - 如果key存在，返回已存在的value和true
   - 如果key不存在，存储value并返回value和false
5. **LoadAndDelete(key interface{}) (value interface{}, loaded bool)**

   - 获取key对应的value并删除该键值对
   - 如果key存在，返回value和true
   - 如果key不存在，返回nil和false
6. **Range(f func(key, value interface{}) bool)**

   - 遍历所有键值对
   - 对每个键值对调用函数f
   - 如果f返回false，则停止遍历
   - 遍历过程中可以安全地删除键值对
7. **CompareAndSwap(key, old, new interface{}) bool**

   - 比较并交换操作
   - 如果key对应的value等于old，则用new替换并返回true
   - 否则返回false
8. **Swap(key, value interface{}) (previous interface{}, loaded bool)**

   - 存储value并返回key之前的值
   - 如果key存在，返回之前的value和true
   - 如果key不存在，返回nil和false

## sync.Map的特点

1. **并发安全**：可以被多个goroutine同时访问
2. **无需初始化**：声明即可使用，不需要make()
3. **适合特定场景**：
   - 当key是固定的，并且多个goroutine分别对不同的key进行读写
   - 当多个goroutine对同一个key进行读取，而很少写入

## sync.Map的基本用法

```
package main

import (
    "fmt"
    "sync"
)

func main() {
    var m sync.Map
    
    // 存储键值对
    m.Store("name", "张三")
    m.Store("age", 25)
    
    // 获取值
    if value, ok := m.Load("name"); ok {
        fmt.Printf("name: %v\n", value)
    }
    
    // 删除键值对
    m.Delete("age")
    
    // 如果键不存在则存储
    m.LoadOrStore("score", 90)
    
    // 遍历所有键值对
    m.Range(func(key, value interface{}) bool {
        fmt.Printf("%v: %v\n", key, value)
        return true // 返回false将停止遍历
    })
}
```

## 实际应用示例

1. **并发计数器**

```
package main

import (
    "fmt"
    "sync"
)

type Counter struct {
    sync.Map
}

func (c *Counter) Increment(key string) {
    for {
        // 获取当前值
        value, loaded := c.LoadOrStore(key, 1)
        if !loaded {
            // 键不存在，已存储初始值1
            return
        }
        
        // 键存在，尝试更新
        count := value.(int)
        if c.CompareAndSwap(key, count, count+1) {
            return
        }
        // 更新失败，重试
    }
}

func main() {
    counter := &Counter{}
    var wg sync.WaitGroup
    
    // 并发增加计数
    for i := 0; i < 100; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            counter.Increment("visits")
        }()
    }
    
    wg.Wait()
    
    // 获取最终计数
    if value, ok := counter.Load("visits"); ok {
        fmt.Printf("总访问次数: %d\n", value)
    }
}
```

2. **并发缓存**

```
// 这是一个嵌入sync.Map的结构体，用于实现并发缓存
type Cache struct {
    sync.Map
}

func (c *Cache) GetOrCompute(key string, compute func() interface{}) interface{} {
    // 尝试从缓存获取
    if value, ok := c.Load(key); ok {
        return value
    }
    
    // 计算新值
    value := compute()
    
    // 存储并返回
    actual, loaded := c.LoadOrStore(key, value)
    if loaded {
        // 其他goroutine已经存储了值
        return actual
    }
    return value
}
```

## sync.Map vs 互斥锁保护的map

在 Go 语言中，`sync.Map` 和使用互斥锁（`sync.Mutex` 或 `sync.RWMutex`）保护的普通 `map` 都可以实现并发安全访问，但它们适用于不同的使用场景。以下是它们之间的详细对比：

| 特性/维度 | `sync.Map` | `map + sync.Mutex` |
| --- | --- | --- |
| **线程安全** | ✅ 内建支持并发 | ✅ 需要手动加锁 |
| **读多写少场景** | 👍 高性能，避免锁开销 | 👎 频繁加锁影响性能 |
| **写多读少场景** | 👎 性能差，底层结构复杂 | 👍 只要锁机制合理，性能较好 |
| **类型安全** | 👎 接口类型 `interface{}`，需要类型断言 | 👍 类型安全，可自定义 map 类型 |
| **API 灵活性** | 👎 API 固定，只有 Store/Load/LoadOrStore/Delete/Range | 👍 可自定义任意方法 |
| **遍历效率** | 👎 `Range` 不保证顺序，可能受内部结构影响 | 👍 遍历 map 更灵活 |
| **内存回收** | 👎 底层采用分段结构，可能残留旧数据较久 | 👍 更容易控制生命周期 |

### 使用场景建议

**适合使用 `sync.Map` 的场景：**

- 配置缓存、字典映射、频繁读取、偶尔写入（**读多写少**）
- 不关心类型断言成本
- 想快速实现并发安全 map
- **如：** 缓存单例对象、用户登录状态、全局只读数据表等

**适合使用 `map + Mutex` 的场景：**

- 类型安全要求高
- 有复杂的业务逻辑和数据结构操作
- 写入频繁或对性能要求稳定
- 想控制锁粒度、读写锁分离优化

### 性能对比（简单结论）

在标准库的 benchmark 中：

- **大量读操作时，`sync.Map` 明显优于 `map + Mutex`**
- **写入操作多时，`sync.Map` 性能不如手动加锁的 map**

# Context包

## 为什么需要 Context？

在并发编程中，我们经常需要在多个 goroutine 之间传递信号或共享数据。例如：

- 取消一个长时间运行的操作
- 设置操作的超时时间
- 在请求处理链中传递用户信息

Context 包就是为了解决这些问题而设计的。

## Context 是什么？

Context（上下文）是 Go 语言中用于在 goroutine 之间传递取消信号、截止时间、键值对等上下文信息的标准包。

主要功能：

1. **取消信号传递**：通知子goroutine停止工作，一般用于手动取消
2. **截止时间控制**：设置操作的最长执行时间，如果超过截止时间，则取消操作
3. **键值对传递**：在不同goroutine间传递请求相关的数据
4. **层级关系管理**：具有父子关系的context树形结构，可以传递上下文信息

## 如何使用 Context？

### 基本示例：context.WithTimeout

```
package main

import (
    "context"
    "fmt"
    "time"
)

func main() {
    // 创建一个2秒超时的 context
    ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
    defer cancel()

    fmt.Println("开始执行程序:", time.Now().Format("15:04:05"))
    
    // 这行会阻塞，直到 queryDatabase 返回或超时
    result, err := queryDatabase(ctx)
    
    fmt.Println("程序执行结束:", time.Now().Format("15:04:05"))
    
    if err == context.DeadlineExceeded {
        fmt.Println("发生超时错误")
    } else if err != nil {
        fmt.Println("发生其他错误:", err)
    } else {
        fmt.Println("获得结果:", result)
    }
}

// 模拟一个耗时的数据库查询
func queryDatabase(ctx context.Context) (string, error) {
    fmt.Println("开始查询数据库...")
    
    select {
    case <-time.After(3 * time.Second):
        // 这个分支会在3秒后执行
        fmt.Println("数据库查询完成")
        return "查询结果", nil
        
    case <-ctx.Done():
        // 这个分支会在 context 被取消时执行
        fmt.Println("数据库查询被取消")
        return "", ctx.Err()
    }
}

// 运行结果：
// 开始执行程序: 15:04:05
// 开始查询数据库...
// 数据库查询被取消
// 程序执行结束: 15:04:07
// 发生超时错误
```

### WithCancel示例

```
package main

import (
    "context"
    "fmt"
    "time"
)

func main() {
    // 创建一个可取消的context
    ctx, cancel := context.WithCancel(context.Background())
    
    // 启动一个工作goroutine
    go worker(ctx)
    
    // 让worker工作3秒
    time.Sleep(3 * time.Second)
    
    // 调用cancel()会发送取消信号
    cancel()
    
    // 等待worker处理完取消信号
    time.Sleep(time.Second)
}

func worker(ctx context.Context) {
    for {
        select {
        case <-ctx.Done(): // 监听取消信号
            fmt.Println("worker收到取消信号，正在退出...")
            return
        default:
            fmt.Println("worker正在工作...")
            time.Sleep(time.Second)
        }
    }
}

// 运行结果：
// worker正在工作...
// worker正在工作...
// worker正在工作...
// worker收到取消信号，正在退出...
```

### WithDeadline示例

```
package main

import (
    "context"
    "fmt"
    "time"
)

func main() {
    // 创建一个截止时间是3秒后的context
    deadline := time.Now().Add(3 * time.Second)
    ctx, cancel := context.WithDeadline(context.Background(), deadline)
    defer cancel() // 良好实践：使用defer确保cancel被调用

    go checkDeadline(ctx)

    // 主程序继续运行5秒，以观察deadline效果
    time.Sleep(5 * time.Second)
}

func checkDeadline(ctx context.Context) {
    // 获取截止时间信息
    deadline, ok := ctx.Deadline()
    if ok {
        fmt.Printf("截止时间是: %v\n", deadline.Format("15:04:05"))
    }

    select {
    case <-ctx.Done():
        // 可以通过ctx.Err()获取context结束的原因
        fmt.Printf("context结束，原因: %v\n", ctx.Err())
    case <-time.After(4 * time.Second):
        fmt.Println("工作完成")
    }
}

// 运行结果：
// 截止时间是: 15:04:08
// context结束，原因: context deadline exceeded
```

### context.WithValue示例

```
package main

import (
    "context"
    "fmt"
    "time"
)

func main() {
    // 创建一个基础context
    baseCtx := context.Background()
    
    // 添加值
    ctx := context.WithValue(baseCtx, "requestID", "req-123")
    
    // 添加超时控制
    ctx, cancel := context.WithTimeout(ctx, 2*time.Second)
    defer cancel()
    
    // 启动工作goroutine
    go doWork(ctx)
    
    // 等待工作完成或超时
    time.Sleep(3 * time.Second)
}

func doWork(ctx context.Context) {
    // 获取请求ID
    requestID := ctx.Value("requestID").(string)
    fmt.Printf("开始处理请求: %s\n", requestID)
    
    for {
        select {
        case <-ctx.Done():
            // 可能是超时，也可能是取消
            fmt.Printf("请求 %s 结束，原因: %v\n", requestID, ctx.Err())
            return
        case <-time.After(500 * time.Millisecond):
            fmt.Printf("请求 %s 正在处理中...\n", requestID)
        }
    }
}

// 运行结果：
// 开始处理请求: req-123
// 请求 req-123 正在处理中...
// 请求 req-123 正在处理中...
// 请求 req-123 正在处理中...
// 请求 req-123 结束，原因: context deadline exceeded
```

## Context 的四种创建方式

| 创建方式 | 主要用途 | 使用场景 |
| --- | --- | --- |
| WithCancel | 手动取消 | 需要手动控制取消时机 |
| WithTimeout | 超时取消 | 需要控制最长执行时间 |
| WithDeadline | 截止时间取消 | 需要在特定时间点取消 |
| WithValue | 传递数据 | 需要传递请求范围的数据 |

## 在请求中使用 Context

```
package main

import (
    "context"
    "fmt"
)

// 定义key类型，避免使用string作为key，防止键名冲突
type contextKey string

// 定义常量作为context的key
const (
    userIDKey   contextKey = "userID"
    userNameKey contextKey = "userName"
)

func main() {
    // 创建基础context
    ctx := context.Background()
    
    // 链式调用WithValue添加多个键值对
    ctx = context.WithValue(ctx, userIDKey, "12345")
    ctx = context.WithValue(ctx, userNameKey, "张三")
    
    // 模拟请求处理链
    handleRequest(ctx)
}

func handleRequest(ctx context.Context) {
    // 第一层处理
    processLayer1(ctx)
}

func processLayer1(ctx context.Context) {
    // 获取context中的值
    userID := ctx.Value(userIDKey).(string)
    userName := ctx.Value(userNameKey).(string)
    
    fmt.Printf("处理用户请求: ID=%s, Name=%s\n", userID, userName)
    
    // 继续处理下一层
    processLayer2(ctx)
}

func processLayer2(ctx context.Context) {
    // 在处理链的任何位置都可以访问context中的值
    userName := ctx.Value(userNameKey).(string)
    fmt.Printf("你好, %s! 请求处理完成\n", userName)
}

// 运行结果：
// 处理用户请求: ID=12345, Name=张三
// 你好, 张三! 请求处理完成
```

## Context 使用的最佳实践

1. **超时控制**

   - 总是为耗时操作设置合理的超时时间
   - 在调用外部服务时必须设置超时
   - 超时时间要根据业务场景合理设置
2. **取消传播**

   - 确保取消信号能够正确传播到所有相关的 goroutine
   - 收到取消信号后要及时清理资源
   - 使用 defer 确保 cancel 函数被调用
3. **值传递**

   - Context 中的值应该限制在请求范围内
   - 不要将函数参数放入 Context 中
   - Context 的值应该是不可变的
4. **错误处理**

   - 正确处理 context.DeadlineExceeded 错误
   - 区分超时错误和其他错误
   - 提供有意义的错误信息

## 小练习：实现带超时的并发下载器

编写一个程序，实现以下功能：

1. 同时下载多个文件
2. 为每个下载任务设置超时时间
3. 当任何一个下载超时，取消所有其他下载
4. 正确处理所有错误情况

# 并发模式和最佳实践

## 工作池模式

工作池模式用于控制并发任务的数量，避免创建过多的goroutine。

```
package main

import (
    "fmt"
    "sync"
    "time"
)

// 任务类型
type Task struct {
    ID int
}

// 工作池
type WorkerPool struct {
    numWorkers int
    tasks      chan Task
    results    chan string
    wg         sync.WaitGroup
}

// 创建新的工作池
func NewWorkerPool(numWorkers int, bufferSize int) *WorkerPool {
    return &WorkerPool{
        numWorkers: numWorkers,
        tasks:      make(chan Task, bufferSize),
        results:    make(chan string, bufferSize),
    }
}

// 启动工作池
func (wp *WorkerPool) Start() {
    for i := 0; i < wp.numWorkers; i++ {
        wp.wg.Add(1)
        go wp.worker(i)
    }
}

// 工作者
func (wp *WorkerPool) worker(id int) {
    defer wp.wg.Done()
    
    for task := range wp.tasks {
        // 处理任务
        result := fmt.Sprintf("Worker %d completed task %d", id, task.ID)
        wp.results <- result
        time.Sleep(100 * time.Millisecond) // 模拟工作耗时
    }
}

// 提交任务
func (wp *WorkerPool) Submit(task Task) {
    wp.tasks <- task
}

// 关闭工作池
func (wp *WorkerPool) Close() {
    close(wp.tasks)
    wp.wg.Wait()
    close(wp.results)
}

func main() {
    // 创建一个有3个工作者的工作池
    pool := NewWorkerPool(3, 10)
    pool.Start()
    
    // 提交10个任务
    go func() {
        for i := 0; i < 10; i++ {
            pool.Submit(Task{ID: i})
        }
        pool.Close()
    }()
    
    // 收集结果
    for result := range pool.results {
        fmt.Println(result)
    }
}
```

**工作池模式是如何控制并发任务的数量的？**

1. **固定数量的工作者**

   - 在创建WorkerPool时，通过`numWorkers`参数指定工作者的数量
   - 这些工作者是固定的，不会动态增加或减少
   - 例如示例代码中创建了3个工作者：`pool := NewWorkerPool(3, 10)`
2. **任务队列缓冲**

   - 使用带缓冲的通道`tasks`来存储待处理的任务
   - 缓冲区大小在创建时指定：`make(chan Task, bufferSize)`
   - 当缓冲区满时，新的任务提交会被阻塞，从而控制任务提交速率
3. **任务分发机制**

   - 所有工作者共享同一个任务通道`tasks`
   - 当任务被提交到通道时，空闲的工作者会自动获取任务
   - 如果所有工作者都在忙，新任务会在通道中等待
4. **资源管理**

   - 使用`sync.WaitGroup`确保所有工作者正确退出
   - 通过`Close()`方法优雅地关闭工作池
   - 确保所有任务都被处理完成后才关闭结果通道

## 扇入扇出模式

扇入扇出模式用于处理多个输入源和输出源的场景。  
例如实际开发中，一个http请求，需要调用多个服务，每个服务返回一个结果，最后将所有结果合并返回。

**示例代码：简单计算数字的平方**

```
package main

import (
	"fmt"
	"sync"
	"time"
)

// worker：处理任务的函数，把输入值平方后输出到结果通道
func worker(id int, jobs <-chan int, results chan<- int, wg *sync.WaitGroup) {
	defer wg.Done()
	for num := range jobs {
		fmt.Printf("Worker %d 处理任务：%d\n", id, num)
		time.Sleep(100 * time.Millisecond) // 模拟处理延迟
		results <- num * num               // 处理结果：平方
	}
}

func main() {
	// 要处理的数字任务
	tasks := []int{1, 2, 3, 4, 5, 6}

	// 创建任务通道和结果通道
	jobs := make(chan int)
	results := make(chan int)

	// 用 WaitGroup 等待所有 worker 完成
	var wg sync.WaitGroup

	// 启动 3 个 worker goroutine（扇出）
	for i := 1; i <= 3; i++ {
		wg.Add(1)
		go worker(i, jobs, results, &wg)
	}

	// 发送任务到 jobs 通道（主 goroutine）
	go func() {
		for _, num := range tasks {
			jobs <- num
		}
		close(jobs) // 所有任务发送完毕，关闭任务通道
	}()

	// 扇入部分：等待所有 worker 完成后，关闭结果通道
	go func() {
		wg.Wait()       // 等所有 worker 完成
		close(results)  // 所有结果已发送完，关闭通道
	}()

	// 主 goroutine 读取结果通道中的数据
	for res := range results {
		fmt.Println("收到结果：", res)
	}
}
```

**概念解释**

| 概念 | 说明 |
| --- | --- |
| **扇出** | `3` 个 worker 并发从 `jobs` 通道中取任务并处理 |
| **扇入** | 所有 worker 把结果放入 `results` 通道中，主 goroutine 从中读取 |
| **通道关闭** | `jobs` 在任务发完后关闭，`results` 在所有 worker 完成后关闭 |
| **同步控制** | `sync.WaitGroup` 保证所有 worker 执行完后再关闭 `results` |

## 总结

Go语言的并发特性强大而灵活，但也需要谨慎使用。在实际开发中：

1. 优先使用channel进行goroutine间通信
2. 合理使用锁保护共享资源
3. 注意避免goroutine泄漏
4. 使用context管理goroutine生命周期
5. 选择合适的并发模式
6. 注意性能优化和资源管理
