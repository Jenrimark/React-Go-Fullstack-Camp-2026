# 映射

来源：
- https://campus.wps.cn/contentpreview/2876b7ef-16e4-4367-80db-48176f8ddd06

# 映射（Map）的使用

# Map 概述

Map 是 Go 语言中的关联数组或字典，是一种无序的键值对的集合。Map 中所有的键必须是相同的类型，所有的值也必须是相同的类型，但键和值的类型可以不同。

Map 是引用类型，因此在传递时，传递的是引用而不是副本。

# Map 的声明和初始化

## 声明 Map

```
// 声明一个键为string类型，值为int类型的map
var scores map[string]int
```

一个被声明但未初始化的 map 的值为`nil`。尝试对一个 nil 的 map 进行操作会导致运行时错误。

## 初始化 Map

```
// 方法1：使用make函数初始化
scores = make(map[string]int)

// 方法2：声明的同时初始化
var scores = map[string]int{
    "张三": 80,
    "李四": 90,
    "王五": 85,
}

// 方法3：短变量声明
scores := map[string]int{
    "张三": 80,
    "李四": 90,
    "王五": 85,
}
```

# Map 的基本操作

## 添加和修改元素

向 map 中添加键值对或修改已有键的值，语法相同：

```
scores["赵六"] = 78  // 添加新元素
scores["张三"] = 82  // 修改已有元素
```

## 获取元素

```
score := scores["张三"]  // 获取张三的分数
```

如果获取一个不存在的键，Go 会返回值类型的零值，如 int 的零值是 0。

## 判断键是否存在

```
score, exists := scores["张三"]
if exists {
    fmt.Println("张三的分数是:", score)
} else {
    fmt.Println("张三的分数不存在")
}
```

## 删除元素

使用内置的`delete`函数删除 map 中的元素：

```
delete(scores, "张三")  // 删除张三的分数
```

如果要删除的键不存在，delete 不会报错，也不会有任何效果。

# 小练习：学生成绩管理

```
package main

import "fmt"

func main() {
    // 创建一个存储学生成绩的map
    studentScores := make(map[string]int)

    // 添加学生成绩
    studentScores["小明"] = 95
    studentScores["小红"] = 88
    studentScores["小李"] = 76

    // 打印所有学生成绩
    fmt.Println("学生成绩列表:")
    for name, score := range studentScores {
        fmt.Printf("%s: %d分\n", name, score)
    }

    // 修改小明的成绩
    studentScores["小明"] = 97
    fmt.Printf("\n修改后小明的成绩: %d分\n", studentScores["小明"])

    // 检查小王是否有成绩
    score, exists := studentScores["小王"]
    if exists {
        fmt.Printf("小王的成绩: %d分\n", score)
    } else {
        fmt.Println("小王的成绩不存在")
    }

    // 删除小李的成绩
    delete(studentScores, "小李")

    // 再次打印所有学生成绩
    fmt.Println("\n更新后的学生成绩列表:")
    for name, score := range studentScores {
        fmt.Printf("%s: %d分\n", name, score)
    }
}
```

尝试运行这段代码，观察 map 的各种操作效果。

# Map 的遍历

使用`for range`循环可以遍历 map 中的所有键值对：

```
for key, value := range scores {
    fmt.Printf("键: %s, 值: %d\n", key, value)
}
```

如果只想遍历键或值：

```
// 只遍历键
for key := range scores {
    fmt.Println("键:", key)
}

// 只遍历值
for _, value := range scores {
    fmt.Println("值:", value)
}
```

注意：map 的遍历顺序是不确定的，每次遍历的顺序可能不同。

# Map 的嵌套

Map 的值可以是另一个 map，从而形成嵌套结构：

```
// 嵌套map: 外层map的键是学生姓名，值是一个map，存储各科目的成绩
studentGrades := map[string]map[string]int{
    "张三": {
        "数学": 90,
        "语文": 85,
        "英语": 78,
    },
    "李四": {
        "数学": 88,
        "语文": 92,
        "英语": 85,
    },
}

// 访问张三的语文成绩
fmt.Println(studentGrades["张三"]["语文"])

// 添加新学生
studentGrades["王五"] = map[string]int{
    "数学": 95,
    "语文": 80,
    "英语": 90,
}
```

# 小练习：商品库存管理

```
package main

import "fmt"

func main() {
    // 创建一个存储商品和价格的map
    inventory := map[string]map[string]float64{
        "电子产品": {
            "手机": 1999.99,
            "电脑": 5999.99,
            "平板": 2399.99,
        },
        "书籍": {
            "Go程序设计": 79.8,
            "算法导论": 108.0,
            "数据结构": 56.5,
        },
    }

    // 打印所有商品
    fmt.Println("商品库存:")
    for category, products := range inventory {
        fmt.Printf("%s:\n", category)
        for name, price := range products {
            fmt.Printf("  - %s: ¥%.2f\n", name, price)
        }
    }

    // 添加新分类和商品
    inventory["服装"] = map[string]float64{
        "T恤": 99.9,
        "牛仔裤": 199.9,
    }

    // 向已有分类添加新商品
    inventory["电子产品"]["耳机"] = 299.9

    // 修改价格
    inventory["书籍"]["Go程序设计"] = 69.9

    fmt.Println("\n更新后的商品库存:")
    for category, products := range inventory {
        fmt.Printf("%s:\n", category)
        for name, price := range products {
            fmt.Printf("  - %s: ¥%.2f\n", name, price)
        }
    }
}
```

# Map 的常见问题与注意事项

## 1. 并发安全问题

Go 的 map 不是并发安全的。如果多个 goroutine 同时对同一个 map 进行写操作，可能会导致数据竞争，最终可能导致程序崩溃。

解决方案：

- 使用互斥锁（sync.Mutex）保护 map
- 使用 sync.Map（Go 1.9 引入），它是并发安全的

```
// 使用互斥锁保护map
var (
    counter = make(map[string]int)
    mutex   = &sync.Mutex{}
)

// 写入操作
mutex.Lock()
counter["someKey"]++
mutex.Unlock()

// 读取操作
mutex.Lock()
count := counter["someKey"]
mutex.Unlock()
```

## 2. Map 容量

虽然可以在创建 map 时指定容量，但 map 会自动增长以容纳更多的键值对。指定初始容量只是一个性能优化手段。

```
// 创建指定容量的map
scores := make(map[string]int, 100)
```

## 3. 不能对 map 元素取地址

以下代码是不合法的：

```
// 错误：不能对map元素取地址
score := &scores["张三"]
```

这是因为 map 可能会在增长时重新分配内存，使得之前获取的地址无效。

# Map 的实际应用场景

## 计数器

```
// 统计字符串中各字符出现的次数
func countCharacters(s string) map[rune]int {
    counter := make(map[rune]int)
    for _, char := range s {
        counter[char]++
    }
    return counter
}

str := "hello, 世界"
counts := countCharacters(str)
for char, count := range counts {
    fmt.Printf("字符 '%c' 出现 %d 次\n", char, count)
}
```

## 缓存

```
// 简单的缓存实现
cache := make(map[string]string)

// 设置缓存
cache["key1"] = "value1"

// 获取缓存
if value, ok := cache["key1"]; ok {
    fmt.Println("缓存命中:", value)
} else {
    fmt.Println("缓存未命中")
}
```

## 分组/过滤

```
// 按年龄分组用户
users := []struct {
    Name string
    Age  int
}{
    {"张三", 20},
    {"李四", 30},
    {"王五", 20},
    {"赵六", 25},
}

groups := make(map[int][]string)
for _, user := range users {
    groups[user.Age] = append(groups[user.Age], user.Name)
}

// 打印分组结果
for age, names := range groups {
    fmt.Printf("%d岁: %v\n", age, names)
}
```

# 小练习：单词频率统计

编写一个程序，统计一段文本中每个单词出现的频率：

```
package main

import (
    "fmt"
    "strings"
)

func main() {
    text := "Go语言是一个开源的编程语言 它能让构造简单 可靠且高效的软件变得容易 Go语言是从2007年末由Robert Griesemer Rob Pike及Ken Thompson开始设计 Go是一个快速 静态类型 编译型语言 但是它又像动态类型的解释型语言"

    // 将文本分割成单词
    words := strings.Fields(text)

    // 创建一个map来存储每个单词出现的次数
    frequency := make(map[string]int)

    // 统计每个单词出现的次数
    for _, word := range words {
        frequency[word]++
    }

    // 打印结果
    fmt.Println("单词频率统计:")
    for word, count := range frequency {
        fmt.Printf("%s: %d次\n", word, count)
    }

    // 找出出现次数最多的单词
    maxWord := ""
    maxCount := 0
    for word, count := range frequency {
        if count > maxCount {
            maxCount = count
            maxWord = word
        }
    }

    fmt.Printf("\n出现次数最多的单词是 \"%s\"，共出现了 %d 次\n", maxWord, maxCount)
}
```

# 总结

- Map 是 Go 语言中的键值对集合，类似于其他语言中的字典或哈希表
- Map 是引用类型，在函数间传递时传递的是引用而非副本
- Map 提供高效的查找、插入和删除操作
- Map 的遍历顺序是不确定的
- Map 不是并发安全的，在并发环境下需要额外的同步措施
- Map 是 Go 语言中非常常用的数据结构，适用于各种需要键值对存储的场景

在实际编程中，map 是一个非常强大和常用的数据结构，掌握 map 的使用对于 Go 语言编程至关重要。

# 其他教程

<https://go.sixue.work/golang/c01/c01_06.html>
