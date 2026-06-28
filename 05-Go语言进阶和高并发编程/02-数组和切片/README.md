# 数组和切片

来源：
- https://campus.wps.cn/contentpreview/6653b06f-c582-451f-bf67-3bb07a6454ab

# 数组与切片

# 数组的定义与初始化

数组是固定长度的同类型元素序列。在 Go 中，数组的长度是类型的一部分，这意味着 `[3]int` 和 `[5]int` 是不同的类型。

```
// 声明数组的几种方式
var arr1 [5]int                // 声明一个长度为5的整型数组，默认值都是0
arr2 := [3]int{1, 2, 3}       // 声明并初始化
arr3 := [...]int{1, 2, 3, 4}  // 让编译器计算长度
arr4 := [5]int{1: 10, 3: 30}  // 使用索引初始化，未指定的元素为0

// 多维数组
matrix := [2][3]int{
    {1, 2, 3},
    {4, 5, 6},
}
```

数组的一些重要特性：

1. 长度固定
2. 值类型（在函数间传递会复制整个数组）
3. 长度是类型的一部分

## 数组的遍历

Go 提供了多种遍历数组的方式：

```
// 1. 使用传统的 for 循环
arr := [5]int{1, 2, 3, 4, 5}
for i := 0; i < len(arr); i++ {
    fmt.Printf("arr[%d] = %d\n", i, arr[i])
}

// 2. 使用 range 关键字（推荐）
for index, value := range arr {
    fmt.Printf("arr[%d] = %d\n", index, value)
}

// 3. 如果只需要值，可以使用下划线忽略索引
for _, value := range arr {
    fmt.Println(value)
}

// 4. 如果只需要索引，可以省略值
for index := range arr {
    fmt.Printf("Index: %d\n", index)
}
```

## 数组的常用操作

```
// 1. 数组比较
arr1 := [3]int{1, 2, 3}
arr2 := [3]int{1, 2, 3}
arr3 := [3]int{3, 2, 1}

fmt.Println(arr1 == arr2)  // true
fmt.Println(arr1 == arr3)  // false

// 2. 数组拷贝
arr4 := arr1  // 创建 arr1 的完整副本

// 3. 数组作为函数参数
func sumArray(arr [5]int) int {
    sum := 0
    for _, value := range arr {
        sum += value
    }
    return sum
}

// 4. 使用指针避免数组拷贝
func modifyArray(arr *[5]int) {
    arr[0] = 100  // 直接修改原数组
}

// 5. 数组切片操作（创建切片）
arr := [5]int{1, 2, 3, 4, 5}
slice1 := arr[1:4]  // [2 3 4]
slice2 := arr[:3]   // [1 2 3]
slice3 := arr[2:]   // [3 4 5]
```

## 多维数组的操作

```
// 1. 创建和初始化二维数组
matrix := [3][4]int{
    {0, 1, 2, 3},    // 第一行
    {4, 5, 6, 7},    // 第二行
    {8, 9, 10, 11},  // 第三行
}

// 2. 遍历二维数组
for i := 0; i < len(matrix); i++ {
    for j := 0; j < len(matrix[i]); j++ {
        fmt.Printf("%d ", matrix[i][j])
    }
    fmt.Println()
}

// 3. 使用 range 遍历二维数组
for _, row := range matrix {
    for _, value := range row {
        fmt.Printf("%d ", value)
    }
    fmt.Println()
}

// 4. 二维数组的函数传递
func processMatrix(m [3][4]int) {
    // 处理二维数组
}

// 5. 使用指针处理大型二维数组
func modifyMatrix(m *[3][4]int) {
    (*m)[0][0] = 100
}
```

## 数组的实际应用示例

让我们看一个使用数组实现简单温度记录器的例子：

```
package main

import (
    "fmt"
    "math"
)

// 温度记录器，记录一周7天的温度
func main() {
    // 声明并初始化一周的温度数组（单位：摄氏度）
    weeklyTemps := [7]float64{28.5, 30.2, 26.8, 27.5, 31.0, 29.8, 27.2}

    // 打印每天温度
    fmt.Println("一周温度记录：")
    days := [7]string{"周一", "周二", "周三", "周四", "周五", "周六", "周日"}
    for i, temp := range weeklyTemps {
        fmt.Printf("%s: %.1f°C\n", days[i], temp)
    }

    // 计算平均温度
    sum := 0.0
    for _, temp := range weeklyTemps {
        sum += temp
    }
    average := sum / float64(len(weeklyTemps))
    fmt.Printf("\n平均温度: %.1f°C\n", average)

    // 找出最高和最低温度
    maxTemp := weeklyTemps[0]
    minTemp := weeklyTemps[0]
    maxDay := 0
    minDay := 0

    for i, temp := range weeklyTemps {
        if temp > maxTemp {
            maxTemp = temp
            maxDay = i
        }
        if temp < minTemp {
            minTemp = temp
            minDay = i
        }
    }

    fmt.Printf("最高温度: %.1f°C (%s)\n", maxTemp, days[maxDay])
    fmt.Printf("最低温度: %.1f°C (%s)\n", minTemp, days[minDay])

    // 计算温差
    tempDiff := maxTemp - minTemp
    fmt.Printf("一周温差: %.1f°C\n", tempDiff)
}
```

**参考：统计高温天数示例**

```
// 统计温度超过阈值的天数
func countHighTempDays(temperatures [7]float64, threshold float64) int {
    count := 0
    for _, temp := range temperatures {
        if temp > threshold {
            count++
        }
    }
    return count
}

// 使用方法：
// hotDays := countHighTempDays(weeklyTemps, 30.0)
// fmt.Printf("温度超过30°C的天数: %d天\n", hotDays)
```

**参考：多城市温度记录示例**

```
func main() {
    // 多个城市的温度记录（北京、上海、广州）
    cityTemps := [3][7]float64{
        {28.5, 30.2, 26.8, 27.5, 31.0, 29.8, 27.2}, // 北京
        {31.2, 32.0, 30.5, 29.8, 33.1, 32.8, 31.5}, // 上海
        {33.5, 34.2, 32.8, 33.0, 35.2, 34.8, 33.5}, // 广州
    }

    cityNames := [3]string{"北京", "上海", "广州"}

    // 计算并比较各城市的平均温度
    fmt.Println("各城市温度统计：")
    fmt.Println("============================")

    var cityAvgTemps [3]float64

    for i, temps := range cityTemps {
        sum := 0.0
        for _, temp := range temps {
            sum += temp
        }
        avg := sum / float64(len(temps))
        cityAvgTemps[i] = avg

        fmt.Printf("%s 平均温度: %.1f°C\n", cityNames[i], avg)
    }

    // 找出最热的城市
    maxAvgTemp := cityAvgTemps[0]
    maxAvgTempCity := 0

    for i, avg := range cityAvgTemps {
        if avg > maxAvgTemp {
            maxAvgTemp = avg
            maxAvgTempCity = i
        }
    }

    fmt.Printf("\n平均温度最高的城市是: %s (%.1f°C)\n",
               cityNames[maxAvgTempCity], cityAvgTemps[maxAvgTempCity])
}
```

# 切片的概念与操作

切片是对数组的抽象，它提供了一个动态的、可变长度的序列。切片是引用类型，它包含三个组件：

- 指针：指向底层数组
- 长度：当前切片的长度
- 容量：从起始位置到底层数组末尾的长度

```
// 声明切片的几种方式
var slice1 []int               // 声明一个空切片，nil切片
slice2 := []int{1, 2, 3}      // 声明并初始化
slice3 := make([]int, 5)      // 使用make创建，长度为5
slice4 := make([]int, 3, 5)   // 长度为3，容量为5

// 从数组创建切片
arr := [5]int{1, 2, 3, 4, 5}
slice5 := arr[1:4]            // [2 3 4]

//切片的长度
fmt.Println(len(slice5))
//切片的容量
fmt.Println(cap(slice5))

s1 := []int{3, 4, 5, 6}
fmt.Println("len s1", len(s1))
fmt.Println("cap s1", cap(s1))

s2 := s1[1:3]
fmt.Println("len s2", len(s2))
fmt.Println("cap s2", cap(s2))

fmt.Println("s2", s2)
```

思考：

- 切片和数组的声明有什么区别？
- 切片和数组在函数传递时的行为有什么区别？
- 切片和数组在遍历时的行为有什么区别？
- 切片和数组在内存中的布局有什么区别？

## 切片的 append 操作

切片的一个关键特性是可以使用 `append` 函数向其添加元素。`append` 函数返回一个新的切片，其中包含原切片的所有元素以及新添加的元素。

```
// 基本的 append 操作
slice := []int{1, 2, 3}
slice = append(slice, 4)       // 添加单个元素: [1 2 3 4]
slice = append(slice, 5, 6, 7) // 添加多个元素: [1 2 3 4 5 6 7]

// 合并两个切片
slice1 := []int{1, 2, 3}
slice2 := []int{4, 5, 6}
slice1 = append(slice1, slice2...) // 使用 ... 展开切片: [1 2 3 4 5 6]

// 在切片中间添加元素
slice = []int{1, 2, 5, 6}
index := 2
// 在索引 2 处插入元素 3 和 4
slice = append(slice[:index], append([]int{3, 4}, slice[index:]...)...)
fmt.Println(slice) // 输出: [1 2 3 4 5 6]

// 删除切片中的元素
slice = []int{1, 2, 3, 4, 5}
// 删除索引 2 处的元素
slice = append(slice[:2], slice[3:]...)
fmt.Println(slice) // 输出: [1 2 4 5]

// append 和容量关系示例
slice = make([]int, 3, 5)
fmt.Printf("切片: %v, 长度: %d, 容量: %d\n", slice, len(slice), cap(slice))

slice = append(slice, 1)
fmt.Printf("添加一个元素后: %v, 长度: %d, 容量: %d\n", slice, len(slice), cap(slice))

slice = append(slice, 2, 3, 4)
fmt.Printf("添加多个元素超出容量后: %v, 长度: %d, 容量: %d\n", slice, len(slice), cap(slice))
```

### append 方法的注意事项

1. 如果切片的容量足够容纳新元素，append 不会创建新的底层数组，而是直接将元素添加到现有数组中。
2. 如果容量不足，Go 会创建一个新的、更大的底层数组，并将所有元素复制到新数组中。
3. 当发生扩容时，新容量通常是旧容量的两倍，但也可能根据切片的大小和状态采用不同的扩容策略。
4. append 返回的是一个新的切片，必须使用返回值（通常是赋值给原切片变量）。
5. 删除切片中的元素没有专门的方法，通常通过 append 组合使用切片表达式来实现。

### 使用 append 的性能考虑

```
// 低效的切片添加方式
slice := []int{}
for i := 0; i < 10000; i++ {
    slice = append(slice, i)
}

// 更高效的方式：预分配足够容量
slice = make([]int, 0, 10000)
for i := 0; i < 10000; i++ {
    slice = append(slice, i)
}
```

在需要添加大量元素时，预先分配足够的容量可以避免多次扩容和内存复制操作，显著提高性能。

## 切片的长度和容量的区别

切片的长度和容量是两个容易混淆但非常重要的概念：

- **长度（Length）**：当前切片中实际存储的元素个数，可以通过 `len()` 函数获取
- **容量（Capacity）**：从切片起始位置到底层数组末尾的元素个数，可以通过 `cap()` 函数获取

让我们通过一个例子来理解：

```
// 创建一个包含10个整数的数组
arr := [10]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
slice := arr[2:5] // 从索引2开始，到索引5结束（不包括5）

// 打印切片的长度和容量
fmt.Println(len(slice)) // 输出：3
fmt.Println(cap(slice)) // 输出：8
```

在这个例子中：

- 长度 `len(slice)` 是 3，因为当前切片中只有 3 个元素：`[3, 4, 5]`
- 容量 `cap(slice)` 是 8，因为从索引 2 开始，到数组末尾还有 8 个元素
- 容量总是大于或等于长度，因为容量是底层数组的总长度减去切片起始位置的索引
- 容量决定了切片可以扩展的最大长度，而长度决定了当前切片中实际存储的元素个数
- 当切片的长度超过其容量时，Go 会自动扩容，创建一个新的底层数组，并将原数据复制到新数组中
- 扩容时，Go 会分配一个新的底层数组，并复制原数据，因此容量会发生变化
- 扩容策略：当切片的长度超过其容量时，Go 会分配一个新的底层数组，并复制原数据，因此容量会发生变化
- 这样设计的好处是：
  - 可以避免切片在扩容时频繁地重新分配底层数组，提高性能
  - 可以更好地控制切片的大小，避免内存泄漏
  - 可以更好地管理底层数组，避免数据越界

## 练习 1：切片操作实践

请完成以下任务：将代码提交到 `week01/practice/slice_practice_01.go` 文件中

1. 创建一个包含 10 个整数的切片，初始值为 1 到 10
2. 使用切片操作获取第 3 到第 7 个元素（包含第 7 个）
3. 在切片末尾添加三个新元素：11, 12, 13
4. 删除切片中的第 5 个元素
5. 将切片中的所有元素乘以 2
6. 打印最终切片的内容和容量

## 切片的实际应用案例

让我们看几个使用切片的实际案例，展示切片在日常编程中的强大功能。

### 案例 1：简单的任务队列

```
package main

import (
    "fmt"
    "time"
)

// 任务结构体
type Task struct {
    ID      int
    Name    string
    Created time.Time
}

// 任务队列
type TaskQueue struct {
    tasks []Task
}

// 创建新队列
func NewTaskQueue() *TaskQueue {
    return &TaskQueue{
        tasks: make([]Task, 0, 10), // 预分配10个任务的容量
    }
}

// 添加任务
func (q *TaskQueue) AddTask(name string) {
    task := Task{
        ID:      len(q.tasks) + 1,
        Name:    name,
        Created: time.Now(),
    }
    q.tasks = append(q.tasks, task)
    fmt.Printf("任务 [%d] %s 已添加\n", task.ID, task.Name)
}

// 获取下一个任务并从队列中移除
func (q *TaskQueue) GetNextTask() (Task, bool) {
    if len(q.tasks) == 0 {
        return Task{}, false
    }

    // 获取第一个任务
    task := q.tasks[0]

    // 将任务从队列中移除 (切片操作)
    q.tasks = q.tasks[1:]

    return task, true
}

// 查看所有任务
func (q *TaskQueue) ListTasks() {
    fmt.Println("当前任务列表:")
    if len(q.tasks) == 0 {
        fmt.Println("  [空]")
        return
    }

    for i, task := range q.tasks {
        fmt.Printf("  %d. [%d] %s (创建于: %s)\n",
            i+1, task.ID, task.Name, task.Created.Format("15:04:05"))
    }
}

func main() {
    queue := NewTaskQueue()

    // 添加一些任务
    queue.AddTask("编写报告")
    queue.AddTask("修复Bug")
    queue.AddTask("开发新功能")

    // 列出所有任务
    queue.ListTasks()

    // 处理任务
    fmt.Println("\n开始处理任务:")
    for i := 0; i < 2; i++ {
        if task, ok := queue.GetNextTask(); ok {
            fmt.Printf("正在处理任务: [%d] %s\n", task.ID, task.Name)
            time.Sleep(500 * time.Millisecond) // 模拟处理时间
        }
    }

    // 再次列出剩余任务
    fmt.Println("\n剩余任务:")
    queue.ListTasks()
}
```

### 案例 2：单词分割与处理

```
package main

import (
    "fmt"
    "strings"
)

// 将文本分割成单词并统计
func processText(text string) map[string]int {
    // 转为小写并分割文本
    text = strings.ToLower(text)
    words := strings.Fields(text) // 使用空白字符分割成切片

    // 统计每个单词出现的次数
    wordCount := make(map[string]int)
    for _, word := range words {
        // 去除标点符号
        word = strings.Trim(word, ",.!?;:()")
        if word != "" {
            wordCount[word]++
        }
    }

    return wordCount
}

// 找出出现频率最高的n个单词
func topNWords(wordCount map[string]int, n int) []string {
    // 创建单词切片
    words := make([]string, 0, len(wordCount))
    for word := range wordCount {
        words = append(words, word)
    }

    // 使用切片对单词进行排序（按出现频率）
    for i := 0; i < len(words)-1; i++ {
        for j := i + 1; j < len(words); j++ {
            if wordCount[words[i]] < wordCount[words[j]] {
                // 交换位置
                words[i], words[j] = words[j], words[i]
            }
        }
    }

    // 返回前n个单词
    if n > len(words) {
        n = len(words)
    }
    return words[:n]
}

func main() {
    text := `Go 语言是一种开源的编程语言，它使得构建简单、可靠且高效的软件变得容易。
             Go语言由Google开发，Go语言专门针对多处理器系统应用程序的编程进行了优化，
             使用Go编译的程序可以媲美C或C++代码的速度，而且更加安全、支持并行进程。`

    // 处理文本
    wordCount := processText(text)

    // 打印所有单词及其出现次数
    fmt.Println("单词统计:")
    for word, count := range wordCount {
        fmt.Printf("  %-10s: %d\n", word, count)
    }

    // 找出出现频率最高的3个单词
    topWords := topNWords(wordCount, 3)
    fmt.Println("\n出现频率最高的3个单词:")
    for i, word := range topWords {
        fmt.Printf("  %d. %s (出现 %d 次)\n", i+1, word, wordCount[word])
    }
}
```

### 案例 3：动态过滤切片

```
package main

import (
    "fmt"
    "strings"
)

// 学生结构体
type Student struct {
    ID    int
    Name  string
    Age   int
    Grade string
    Score float64
}

// 根据年龄过滤学生
func filterByAge(students []Student, minAge, maxAge int) []Student {
    result := make([]Student, 0)

    for _, student := range students {
        if student.Age >= minAge && student.Age <= maxAge {
            result = append(result, student)
        }
    }

    return result
}

// 根据成绩过滤学生
func filterByScore(students []Student, minScore float64) []Student {
    result := make([]Student, 0)

    for _, student := range students {
        if student.Score >= minScore {
            result = append(result, student)
        }
    }

    return result
}

// 根据名字前缀过滤学生
func filterByNamePrefix(students []Student, prefix string) []Student {
    result := make([]Student, 0)

    for _, student := range students {
        if strings.HasPrefix(student.Name, prefix) {
            result = append(result, student)
        }
    }

    return result
}

// 打印学生列表
func printStudents(title string, students []Student) {
    fmt.Printf("\n%s (共 %d 名学生):\n", title, len(students))
    if len(students) == 0 {
        fmt.Println("  [无匹配学生]")
        return
    }

    fmt.Println("  ID  | 姓名   | 年龄 | 班级 | 分数")
    fmt.Println("  ----|--------|-----|------|------")
    for _, s := range students {
        fmt.Printf("  %3d | %-6s | %3d | %-4s | %.1f\n",
                  s.ID, s.Name, s.Age, s.Grade, s.Score)
    }
}

func main() {
    // 创建学生切片
    students := []Student{
        {101, "张三", 18, "高三", 92.5},
        {102, "李四", 17, "高二", 85.0},
        {103, "王五", 16, "高一", 78.5},
        {104, "赵六", 18, "高三", 96.0},
        {105, "钱七", 17, "高二", 88.0},
        {106, "孙八", 16, "高一", 91.5},
        {107, "周九", 18, "高三", 82.0},
        {108, "吴十", 17, "高二", 79.5},
        {109, "郑十一", 16, "高一", 93.0},
        {110, "王明", 18, "高三", 87.5},
    }

    // 打印所有学生
    printStudents("所有学生", students)

    // 过滤18岁的学生
    age18Students := filterByAge(students, 18, 18)
    printStudents("18岁的学生", age18Students)

    // 过滤90分以上的学生
    highScoreStudents := filterByScore(students, 90.0)
    printStudents("90分以上的学生", highScoreStudents)

    // 过滤名字以"王"开头的学生
    wangStudents := filterByNamePrefix(students, "王")
    printStudents("姓王的学生", wangStudents)

    // 组合过滤：18岁且90分以上的学生
    elite := filterByScore(filterByAge(students, 18, 18), 90.0)
    printStudents("18岁且90分以上的精英学生", elite)
}
```

# 切片的底层实现

切片的底层实现是一个结构体，包含三个字段：

```
type slice struct {
    array unsafe.Pointer // 指向底层数组的指针
    len   int           // 切片的长度
    cap   int           // 切片的容量
}
```

这个结构解释了为什么切片是引用类型：当我们传递切片时，我们只是复制了这个结构体（24 字节），而不是底层数组的全部数据。

## 切片的内存布局

下面是切片的内存布局示意图：

```
                  +---+---+---+---+---+---+
底层数组:           | 1 | 2 | 3 | 4 | 5 | 6 |
                  +---+---+---+---+---+---+
                    ^
                    |
                  +--------+------+------+
切片结构:          | 指针   | 长度 | 容量 |
                  +--------+------+------+
                  | 数组地址 |  4   |  6   |
                  +--------+------+------+
```

当我们创建一个切片 `s := []int{1, 2, 3, 4}`，Go 会：

1. 分配一个底层数组
2. 创建一个切片结构体，指向该数组
3. 设置长度和容量

## 切片扩容的详细过程

当我们使用 `append` 向切片添加元素，如果容量不足，Go 会：

1. 分配一个新的、更大的底层数组
2. 将原数组的内容复制到新数组
3. 创建一个新的切片结构体，指向新数组
4. 返回这个新的切片

```
// 演示切片扩容过程
func demonstrateAppend() {
    s := make([]int, 3, 5)
    s[0], s[1], s[2] = 1, 2, 3

    fmt.Printf("切片s: %v, 长度: %d, 容量: %d, 地址: %p\n",
               s, len(s), cap(s), &s[0])

    // 添加元素，但不超过容量
    s = append(s, 4)
    fmt.Printf("添加一个元素后 - 切片s: %v, 长度: %d, 容量: %d, 地址: %p\n",
               s, len(s), cap(s), &s[0])

    // 添加元素，超过容量
    s = append(s, 5, 6)
    fmt.Printf("添加两个元素后 - 切片s: %v, 长度: %d, 容量: %d, 地址: %p\n",
               s, len(s), cap(s), &s[0])
}
```

执行这段代码，你会发现第二次 `append` 后，切片的地址发生了变化，这表明底层数组被重新分配了。

# 数组与切片的区别

让我们通过一个表格来对比数组和切片的主要区别：

| 特性 | 数组 | 切片 |
| --- | --- | --- |
| 长度 | 固定长度 | 动态长度 |
| 类型 | 值类型 | 引用类型 |
| 传递方式 | 传值（复制整个数组） | 传引用（复制切片结构） |
| 内存分配 | 栈或堆 | 总是在堆上 |
| 声明方式 | `[n]T` | `[]T` |
| 大小比较 | 相同类型可以直接比较 | 不能直接比较 |

让我们通过一个例子来说明这些区别：

```
package main

import "fmt"

func modifyArray(arr [3]int) {
    arr[0] = 100    // 修改的是副本，不会影响原数组
}

func modifySlice(slice []int) {
    slice[0] = 100  // 修改的是底层数组，会影响原切片
}

func main() {
    // 数组示例
    arr := [3]int{1, 2, 3}
    modifyArray(arr)
    fmt.Println("数组:", arr)  // 输出: [1 2 3]

    // 切片示例
    slice := []int{1, 2, 3}
    modifySlice(slice)
    fmt.Println("切片:", slice)  // 输出: [100 2 3]

    // 内存效率对比
    var bigArray [1000000]int  // 在栈上分配大量内存，可能导致栈溢出
    bigSlice := make([]int, 1000000)  // 在堆上分配内存，更安全

    // 长度和容量
    fmt.Printf("数组：长度=%d\n", len(arr))  // 长度=3
    fmt.Printf("切片：长度=%d，容量=%d\n", len(slice), cap(slice))  // 长度=3，容量>=3
}
```

一些关键注意点：

1. **内存效率**：

   - 大型数组作为函数参数传递时会复制整个数组，效率低
   - 切片传递只会复制切片头部结构（24 字节），效率高
2. **灵活性**：

   - 数组的长度固定，适合存储固定大小的数据
   - 切片可以动态增长，适合存储不确定大小的数据
3. **初始化**：

   - 数组必须指定大小或使用 `...` 让编译器推断
   - 切片可以使用 `make` 函数指定长度和容量，更灵活
4. **比较操作**：

   - 数组可以用 `==` 直接比较（同类型数组）
   - 切片只能与 `nil` 比较，不能相互比较

## 何时使用数组，何时使用切片？

- **使用数组的情况**：

  - 当你确切知道元素数量且不会改变
  - 当你需要在栈上分配内存（小型数组）
  - 当你需要比较两个集合是否完全相同
- **使用切片的情况**：

  - 当元素数量可能变化
  - 当你需要传递大量数据但不想复制
  - 当你需要对数据进行添加、删除等操作
  - 大多数情况下（Go 中切片比数组更常用）

# 对数组或切片进行排序

在 Go 语言中，对数组或切片进行排序可以使用标准库中的 `sort` 包。`sort` 包提供了多种排序函数，适用于不同类型的数组和切片。以下是几种常见的排序场景：

## 对整数切片排序

```
package main

import (
    "fmt"
    "sort"
)

func main() {
    numbers := []int{5, 2, 8, 1, 9}
    sort.Ints(numbers)
    fmt.Println(numbers)
}
```

在上述代码中，使用 `sort.Ints` 函数对 `int` 类型的切片 `numbers` 进行排序。`sort.Ints` 函数会对传入的切片进行原地排序，即直接修改原切片的元素顺序。

## 对字符串切片排序

```
package main

import (
    "fmt"
    "sort"
)

func main() {
    words := []string{"banana", "apple", "cherry"}
    sort.Strings(words)
    fmt.Println(words)
}
```

这里使用 `sort.Strings` 函数对 `string` 类型的切片 `words` 进行排序。同样，该函数也是原地排序。

## 对自定义类型切片排序

如果要对自定义类型的切片进行排序，需要实现 `sort.Interface` 接口。该接口包含三个方法：`Len`、`Less` 和 `Swap`。

```
package main

import (
	"fmt"
	"sort"
)

type Person struct {
	Name string
	Age  int
}

// ByAge 用于按年龄对Person切片进行排序
type ByAge []Person

func (a ByAge) Len() int           { return len(a) }
func (a ByAge) Less(i, j int) bool { return a[i].Age < a[j].Age }
func (a ByAge) Swap(i, j int)      { a[i], a[j] = a[j], a[i] }

func main() {
	people := []Person{
		{"Alice", 25},
		{"Bob", 20},
		{"Charlie", 30},
	}

	sort.Sort(ByAge(people))
	fmt.Println(people)

	//也可以使用slice再次排序
	sort.Slice(people, func(i, j int) bool {
		return people[i].Age > people[j].Age
	})
	fmt.Println(people)
}
```

在这个例子中：

1. 定义了一个 `Person` 结构体。
2. 创建了一个 `ByAge` 类型，它是 `Person` 切片的别名，并为 `ByAge` 实现了 `sort.Interface` 接口的三个方法。
3. 在 `main` 函数中，创建了一个 `Person` 切片，然后使用 `sort.Sort` 函数对其进行排序，`sort.Sort` 函数接受一个实现了 `sort.Interface` 接口的类型。

### 对浮点数切片排序

Go 标准库中没有专门针对 `float64` 切片的排序函数，但可以通过实现 `sort.Interface` 接口来实现。

```
package main

import (
    "fmt"
    "sort"
)

func main() {
    floats := []float64{3.14, 1.618, 2.718}
    sort.Slice(floats, func(i, j int) bool {
        return floats[i] < floats[j]
    })
    fmt.Println(floats)
}
```

这里使用 `sort.Slice` 函数，它接受一个切片和一个比较函数。比较函数定义了如何比较切片中的两个元素，从而实现对浮点数切片的排序。`sort.Slice` 函数内部也是通过调用 `sort.Interface` 接口的方法来实现排序的。

# 其他教程

[数据类型：数组与切片](https://go.sixue.work/golang/c01/c01_05.html)

[值类型和引用类型](https://go.sixue.work/golang/c02/c02_12)

[思考题：切片作为函数参数是传值还是传引用？](https://juejin.cn/post/6888117219213967368)
