# 结构体，方法和JSON处理

来源：
- https://campus.wps.cn/contentpreview/e8cbdf08-853b-4355-9ff0-af8db40a7a4d

# 结构体与json

# 结构体

Go 语言中的基础数据类型可以表示一些事物的基本属性，但是当我们想表达一个事物的全部或部分属性时，这时候再用单一的基本数据类型明显就无法满足需求了，Go 语言提供了一种自定义数据类型，可以封装多个基本数据类型，这种数据类型叫结构体，英文名称 struct。 也就是我们可以通过 struct 来定义自己的类型了。

Go 语言中通过 struct 来实现面向对象。

## 结构体定义

结构体是 Go 语言中一种自定义的数据类型，它可以将不同类型的数据组合在一起。结构体的定义使用 `struct` 关键字。

```
package main

import "fmt"

// 定义一个名为Person的结构体
type Person struct {
    Name string
    Age  int
    City string
}

// 同样类型的字段也可以写在一行，
type person1 struct {
        name, city string
        age        int8
    }
```

在上述代码中，我们定义了一个 `Person` 结构体，它包含三个字段：`Name`（字符串类型）、`Age`（整数类型）和 `City`（字符串类型）。

语言内置的基础数据类型是用来描述一个值的，而结构体是用来描述一组值的。比如一个人有名字、年龄和居住城市等，本质上是一种聚合型的数据类型。

## 结构体的高级特性

### 1. 匿名结构体

在某些场景下，我们可能只需要临时使用一次的结构体：

```
point := struct {
    x, y int
}{
    x: 10,
    y: 20,
}
```

### 2. 结构体嵌套（组合）

Go 通过结构体嵌套实现继承的效果：

```
type Address struct {
    City    string
    Country string
}

type Employee struct {
    Person   // 匿名嵌套，实现继承
    Address  // 匿名嵌套
    Salary   float64
    Position string
}
```

### 3. 结构体标签

结构体标签提供了字段的元信息，常用于序列化和验证：

```
type User struct {
    ID        int    `json:"id" validate:"required"`
    Username  string `json:"username" validate:"required,min=4,max=20"`
    Email     string `json:"email" validate:"required,email"`
    CreatedAt time.Time `json:"created_at,omitempty"`
}
```

### 4. 空结构体的使用

在 Go 语言中，一个没有任何字段的结构体（也称为空结构体 `struct{}`）虽然看似没有数据存储，但在实际编程中有多种重要用途：

#### 1. 节省内存

- **集合成员表示**：当你只关心某个元素是否存在于集合中，而不需要存储关于该元素的任何额外数据时，空结构体非常有用。例如，在一个需要判断一组唯一标识符是否存在的场景中，可以使用 `map` 结合空结构体来实现。

```
package main

import (
    "fmt"
)

func main() {
    // 使用map和空结构体来表示集合
    uniqueIDs := make(map[string]struct{})
    id1 := "123"
    id2 := "456"

    // 将ID添加到集合中
    uniqueIDs[id1] = struct{}{}
    uniqueIDs[id2] = struct{}{}

    // 检查ID是否存在
    if _, exists := uniqueIDs["123"]; exists {
        fmt.Println("ID 123 exists in the set.")
    }
}
```

- 在这个例子中，`map` 的值类型为 `struct{}`，它不占用额外的内存空间，仅作为一种占位符表示键（即 ID）存在于集合中。相比使用 `map[string]bool`，使用 `map[string]struct{}` 可以节省内存，因为 `bool` 类型至少占用一个字节，而空结构体不占用任何内存。

#### 2. 信号传递

- **通道通信**：在通过通道进行通信时，空结构体可以作为简单的信号来传递，用于通知某个事件的发生，而不需要传递任何具体的数据。例如，在一个并发程序中，一个 goroutine 完成任务后向另一个 goroutine 发送信号。

```
package main

import (
    "fmt"
    "time"
)

func worker(done chan struct{}) {
    // 模拟工作
    time.Sleep(2 * time.Second)
    fmt.Println("Worker finished")
    // 发送完成信号
    done <- struct{}{}
}

func main() {
    done := make(chan struct{})
    go worker(done)

    // 等待工作完成信号
    <-done
    fmt.Println("Main function received done signal")
}
```

- 在这个例子中，`worker` goroutine 完成工作后，通过向 `done` 通道发送一个空结构体值来通知主 goroutine 工作已完成。主 goroutine 通过从 `done` 通道接收数据来等待这个信号，这里空结构体仅作为信号标识，不需要携带任何数据。

#### 3. 实现接口

- **满足接口要求**：有时候，你可能需要实现某个接口，但该接口的方法不需要访问结构体的任何字段。在这种情况下，可以使用空结构体作为实现该接口的类型。例如，假设存在一个 `Notifier` 接口，要求实现 `Notify` 方法，而该方法不需要任何状态信息。

```
package main

import "fmt"

type Notifier interface {
    Notify()
}

// 使用空结构体实现Notifier接口
type EmptyNotifier struct{}

func (e EmptyNotifier) Notify() {
    fmt.Println("Notification sent.")
}

func main() {
    var n Notifier = EmptyNotifier{}
    n.Notify()
}
```

- 在这个例子中，`EmptyNotifier` 结构体没有任何字段，但它实现了 `Notifier` 接口的 `Notify` 方法。这种方式允许在不需要存储任何数据的情况下实现接口功能。

总之，虽然空结构体没有字段，但在内存优化、并发编程和接口实现等场景中具有独特的价值。

## 结构体实例化

只有当结构体实例化时，才会真正地分配内存。也就是必须实例化后才能使用结构体的字段。

```
// 1. 使用var声明
var p1 Person

// 2. 使用字面量
p2 := Person{
    Name: "Alice",
    Age:  30,
    City: "New York",
}

// 3. 使用new关键字
p3 := new(Person)

// 4. 使用make函数（用于slice, map, channel）
```

## 结构体初始化的其他方式

### 1. 键值对初始化

```
p4 := Person{
    Name: "张三",
    Age:  18,
}
```

### 2. 值的列表初始化

```
p5 := Person{
    "李四",
    20,
    "北京",
}
```

注意：这种方式必须初始化结构体的所有字段，且顺序必须与结构体定义时字段的顺序一致。不建议使用这种方式。

## 打印结构体内容

```
// 打印结构体内容
fmt.Printf("%+v\n", p1)

// 打印结构体类型和内容
fmt.Printf("%#v\n", p1)
```

> 作用：使用%#v 格式化输出时，会以 Go 语言语法表示的方式输出变量的值，即输出变量的类型和具体的结构，使输出结果更清晰、便于理解。对于结构体类型的变量，这种格式化输出能直观地展示出结构体的字段名和对应的值 。

## 结构体与指针

### 1. 结构体指针的创建

在 Go 语言中，我们有多种方式创建结构体指针：

```
// 1. 使用new关键字
p1 := new(Person)
p1.Name = "张三"  // Go语言中，结构体指针可以直接使用点号访问成员

// 2. 对结构体取地址
p2 := &Person{}
p2.Name = "李四"

// 3. 在创建时直接指定字段值
p3 := &Person{
    Name: "王五",
    Age:  25,
    City: "上海",
}
```

### 2. 结构体指针的传递

当我们需要在函数中修改结构体的值时，应该传递结构体的指针：

```
func modifyPerson(p *Person) {
    p.Name = "新名字"  // 直接修改原结构体的值
}

func main() {
    p := &Person{Name: "旧名字"}
    modifyPerson(p)
    fmt.Println(p.Name)  // 输出: 新名字
}
```

### 3. 结构体指针的注意事项

1. 空指针问题：

```
var p *Person
// p.Name = "张三"  // 错误：panic: runtime error: invalid memory address or nil pointer dereference
```

2. 指针数组：

```
persons := []*Person{
    &Person{Name: "张三"},
    &Person{Name: "李四"},
}
for _, p := range persons {
    fmt.Println(p.Name)
}
```

3. 结构体指针的比较：

```
p1 := &Person{Name: "张三"}
p2 := &Person{Name: "张三"}
fmt.Println(p1 == p2)      // false：比较的是指针地址
fmt.Println(*p1 == *p2)    // true：比较的是结构体的值
```

### 4. 何时使用结构体指针

1. 需要修改结构体的值时
2. 结构体较大时（避免值拷贝）
3. 实现接口时（通常使用指针接收者）

```
type Animal interface {
    Speak() string
}

type Dog struct {
    Name string
}

// 使用指针接收者实现接口
func (d *Dog) Speak() string {
    return d.Name + "说：汪汪！"
}
```

### 5. 内存布局

结构体指针的内存布局示例：

```
type Point struct {
    X, Y int
}

func main() {
    p := &Point{1, 2}
    fmt.Printf("指针大小：%d字节\n", unsafe.Sizeof(p))     // 在64位系统上通常是8字节
    fmt.Printf("结构体大小：%d字节\n", unsafe.Sizeof(*p))  // 16字节（两个int）
}
```

## 结构体的匿名字段

结构体允许其成员字段在声明时没有字段名而只有类型，这种字段被称为匿名字段。

```
type Person struct {
    string
    int
}

func main() {
    p1 := Person{"张三", 18}
    fmt.Printf("姓名:%s 年龄:%d\n", p1.string, p1.int)
}
```

匿名字段默认采用类型名作为字段名，结构体要求字段名称必须唯一，因此一个结构体中同种类型的匿名字段只能有一个。

## 结构体的比较和拷贝

### 1. 结构体比较

结构体的比较规则：

- 只有所有字段都是可比较的，结构体才是可比较的
- 两个结构体的所有字段相等，则这两个结构体相等

```
type Point struct {
    X, Y int
}

p1 := Point{1, 2}
p2 := Point{1, 2}
fmt.Println(p1 == p2) // true
```

### 2. 深浅拷贝

```
type Person struct {
    Name string
    Age  int
    Friends []string  // 切片是引用类型
}

// 浅拷贝
p1 := Person{Name: "Alice", Age: 30, Friends: []string{"Bob"}}
p2 := p1  // p2和p1共享Friends切片

// 深拷贝
p3 := Person{
    Name: p1.Name,
    Age: p1.Age,
    Friends: make([]string, len(p1.Friends)),
}
copy(p3.Friends, p1.Friends)
```

# 方法

## 方法定义

Go 语言中的方法（Method）是一种作用于特定类型变量的函数。这种特定类型变量叫做接收者（Receiver）。接收者的概念就类似于其他语言中的 this 或者 self。

方法是与特定类型（通常是结构体）关联的函数。方法的定义使用接收器（receiver）来指定该方法属于哪个类型。

方法与函数的区别是，函数不属于任何类型，方法属于特定的类型。

```
package main

import "fmt"

type Person struct {
    Name string
    Age  int
    City string
}

// 为Person结构体定义一个方法
func (p Person) Introduce() {
    fmt.Printf("大家好，我叫 %s，今年 %d 岁，住在 %s。\n", p.Name, p.Age, p.City)
}
```

在上述代码中，我们为 `Person` 结构体定义了一个 `Introduce` 方法。接收器 `(p Person)` 表示这个方法属于 `Person` 类型，`p` 是接收器变量，在方法内部可以通过它来访问结构体的字段。

1.接收者变量：接收者中的参数变量名在命名时，官方建议使用接收者类型名的第一个小写字母，而不是 self、this 之类的命名。例如，Person 类型的接收者变量应该命名为 p，Connector 类型的接收者变量应该命名为 c 等。

2.接收者类型：接收者类型和参数类似，可以是指针类型和非指针类型。

3.方法名、参数列表、返回参数：具体格式与函数定义相同。

## 指针类型的接收者

当方法的接收者是指针类型时，我们可以在方法中修改接收者的值。

### 1. 值类型和指针类型接收者的区别

```
type Person struct {
    Name string
    Age  int
}

// 值类型接收者
func (p Person) ModifyAge1(newAge int) {
    p.Age = newAge  // 这个修改只是针对副本，不会影响原来的值
}

// 指针类型接收者
func (p *Person) ModifyAge2(newAge int) {
    p.Age = newAge  // 这个修改会影响原来的值
}

func main() {
    p1 := Person{
        Name: "张三",
        Age:  20,
    }

    p1.ModifyAge1(30)
    fmt.Printf("ModifyAge1后的年龄：%d\n", p1.Age) // 输出：20

    p1.ModifyAge2(30)
    fmt.Printf("ModifyAge2后的年龄：%d\n", p1.Age) // 输出：30
}
```

### 2. 什么时候使用指针接收者

以下情况应该使用指针接收者：

1. 需要在方法中修改接收者的值
2. 接收者是一个大的结构体，使用指针可以避免值拷贝
3. 保持一致性：如果有某个方法使用了指针接收者，那么其他方法也应该使用指针接收者

### 3. 实际案例：学生成绩管理

```
type Student struct {
    ID     int
    Name   string
    Scores map[string]float64
}

// 初始化方法
func NewStudent(id int, name string) *Student {
    return &Student{
        ID:     id,
        Name:   name,
        Scores: make(map[string]float64),
    }
}

// 添加或更新成绩
func (s *Student) SetScore(subject string, score float64) {
    if s.Scores == nil {
        s.Scores = make(map[string]float64)
    }
    s.Scores[subject] = score
}

// 获取平均分
func (s *Student) GetAverageScore() float64 {
    if len(s.Scores) == 0 {
        return 0
    }

    total := 0.0
    for _, score := range s.Scores {
        total += score
    }
    return total / float64(len(s.Scores))
}

func main() {
    // 创建学生实例
    stu := NewStudent(1, "李四")

    // 添加成绩
    stu.SetScore("数学", 95)
    stu.SetScore("语文", 88)
    stu.SetScore("英语", 92)

    // 计算平均分
    avg := stu.GetAverageScore()
    fmt.Printf("%s的平均分是：%.2f\n", stu.Name, avg)
}
```

### 4. 注意事项

1. Go 语言会自动处理指针和值的转换：

```
type Person struct {
    Name string
}

func (p *Person) SayHello() {
    fmt.Printf("Hello, I am %s\n", p.Name)
}

func main() {
    p1 := Person{Name: "张三"}
    p1.SayHello()  // Go会自动转换为(&p1).SayHello()

    p2 := &Person{Name: "李四"}
    p2.SayHello()  // 直接调用指针方法
}
```

2. nil 指针也可以调用方法：

```
type Person struct {
    Name string
}

func (p *Person) GetName() string {
    if p == nil {
        return "匿名"
    }
    return p.Name
}

func main() {
    var p *Person
    fmt.Println(p.GetName()) // 输出：匿名
}
```

3. 方法集的规则：

- 类型 T 的方法集包含所有接收者为 T 的方法
- 类型 \*T 的方法集包含所有接收者为 T 和 \*T 的方法

## 方法的高级特性

### 1. 方法继承

通过结构体嵌套，可以实现方法的继承：

```
type Animal struct {
    Name string
}

func (a Animal) Speak() string {
    return fmt.Sprintf("我是%s", a.Name)
}

type Dog struct {
    Animal  // 继承Animal的方法
    Breed string
}

// Dog可以重写继承的方法
func (d Dog) Speak() string {
    return fmt.Sprintf("汪汪，%s", d.Animal.Speak())
}
```

### 2. 方法表达式和方法值

```
type Rectangle struct {
    Width, Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

func main() {
    r := Rectangle{Width: 10, Height: 5}

    // 方法值
    area := r.Area
    fmt.Println(area())  // 50

    // 方法表达式
    areaFn := Rectangle.Area
    fmt.Println(areaFn(r))  // 50
}
```

### 3. 方法集

方法集定义了一个类型的接口：

- 类型 T 的方法集包含所有接收者为 T 的方法
- 类型 \*T 的方法集包含所有接收者为 T 和 \*T 的方法

# JSON 处理

在 Go 语言中，JSON（JavaScript Object Notation）是一种常用的数据格式，用于在不同系统之间进行数据交换。Go 语言提供了标准库 `encoding/json`，用于处理 JSON 数据。

## 一、JSON 基本概念

JSON 是一种轻量级的数据交换格式，易于阅读和编写，同时也易于机器解析和生成。它基于 JavaScript 编程语言的一个子集，但 JSON 是独立于语言的文本格式，并且采用了类似于 C 语言家族的习惯（包括 C、C++、C#、Java、JavaScript、Perl、Python 等）。

JSON 数据由键值对组成，其中键是字符串，值可以是以下几种类型之一：

- 字符串（String）
- 数字（Number）
- 布尔值（Boolean）
- 数组（Array）
- 对象（Object）
- null

以下是一个简单的 JSON 示例：

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

## 二、Go 语言中处理 JSON 数据

### （一）导入 `encoding/json` 包

在使用 Go 语言处理 JSON 数据之前，需要先导入 `encoding/json` 包。

```
import "encoding/json"
```

### （二）将 Go 结构体转换为 JSON 格式

在 Go 语言中，可以使用结构体来表示 JSON 数据。通过使用 `json.Marshal` 函数，可以将 Go 结构体转换为 JSON 格式的字节切片。

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

在上述代码中，定义了一个 `Person` 结构体，包含 `Name`、`Age` 和 `Hobbies` 三个字段。通过使用 `json.Marshal` 函数，将 `Person` 结构体转换为 JSON 格式的字节切片。如果转换过程中发生错误，会打印错误信息并返回。最后，将 JSON 格式的字节切片转换为字符串并输出。

需要注意的是，在结构体字段上使用了 `json:"name"` 等标签，用于指定结构体字段在 JSON 中的名称。如果不指定标签，默认使用结构体字段的名称作为 JSON 中的键。

### （三）将 JSON 格式转换为 Go 结构体

在 Go 语言中，可以使用 `json.Unmarshal` 函数将 JSON 格式的字节切片转换为 Go 结构体。

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

在上述代码中，定义了一个 `Person` 结构体，包含 `Name`、`Age` 和 `Hobbies` 三个字段。通过使用 `json.Unmarshal` 函数，将 JSON 格式的字节切片转换为 `Person` 结构体。如果转换过程中发生错误，会打印错误信息并返回。最后，输出 `Person` 结构体的字段值。

需要注意的是，在使用 `json.Unmarshal` 函数时，需要传入一个指向结构体的指针作为第二个参数，用于接收转换后的结构体数据。

### （四）处理 JSON 数组

在 Go 语言中，可以使用切片来表示 JSON 数组。通过使用 `json.Unmarshal` 函数，可以将 JSON 格式的字节切片转换为 Go 切片。

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

在上述代码中，定义了一个 `Person` 结构体，包含 `Name`、`Age` 和 `Hobbies` 三个字段。通过使用 `json.Unmarshal` 函数，将 JSON 格式的字节切片转换为 `Person` 结构体切片。如果转换过程中发生错误，会打印错误信息并返回。最后，遍历 `Person` 结构体切片，输出每个结构体的字段值。

### （五）格式化 JSON 输出

在 Go 语言中，可以使用 `json.MarshalIndent` 函数将 Go 结构体转换为格式化的 JSON 格式的字节切片。

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

在上述代码中，定义了一个 `Person` 结构体，包含 `Name`、`Age` 和 `Hobbies` 三个字段。通过使用 `json.MarshalIndent` 函数，将 `Person` 结构体转换为格式化的 JSON 格式的字节切片。其中，第一个参数是要转换的结构体，第二个参数是缩进的前缀，第三个参数是缩进的宽度。如果转换过程中发生错误，会打印错误信息并返回。最后，将格式化的 JSON 格式的字节切片转换为字符串并输出。

## JSON 的高级特性

### 1. 自定义 JSON 序列化

```
type Date struct {
    time.Time
}

func (d Date) MarshalJSON() ([]byte, error) {
    return []byte(fmt.Sprintf(`"%s"`, d.Format("2006-01-02"))), nil
}

func (d *Date) UnmarshalJSON(data []byte) error {
    // 去掉引号
    str := string(data)[1 : len(data)-1]
    parsed, err := time.Parse("2006-01-02", str)
    if err != nil {
        return err
    }
    d.Time = parsed
    return nil
}
```

### 2. JSON 字段控制

```
type User struct {
    ID        int       `json:"id"`
    Username  string    `json:"username"`
    Password  string    `json:"-"`                     // 永远不会被序列化
    Email     string    `json:"email,omitempty"`       // 为空时省略
    CreatedAt time.Time `json:"created_at,string"`     // 序列化为字符串
    UpdatedAt time.Time `json:"updated_at,omitempty"`  // 为空时省略
    Address   *string   `json:"address,omitempty"`     // 指针类型，为nil时省略
}
```

### 3. 处理动态 JSON

```
// 处理未知结构的JSON
var data map[string]interface{}
json.Unmarshal([]byte(`{"name": "Alice", "age": 30}`), &data)

// 使用json.RawMessage延迟解析
type Message struct {
    Type    string          `json:"type"`
    Content json.RawMessage `json:"content"`
}
```

### 4. 高效的 JSON 处理

```
// 使用json.Decoder处理大型JSON文件
file, _ := os.Open("large.json")
defer file.Close()

decoder := json.NewDecoder(file)
for decoder.More() {
    var user User
    if err := decoder.Decode(&user); err != nil {
        log.Fatal(err)
    }
    // 处理user...
}

// 使用json.Encoder流式写入
encoder := json.NewEncoder(os.Stdout)
encoder.SetIndent("", "  ")
encoder.Encode(data)
```

# 实践项目：学生管理系统

让我们通过一个完整的项目来练习结构体、方法和 JSON 的使用：

```
package main

import (
    "encoding/json"
    "fmt"
    "time"
)

type Student struct {
    ID        int       `json:"id"`
    Name      string    `json:"name"`
    Grade     int       `json:"grade"`
    Scores    []Score   `json:"scores,omitempty"`
    CreatedAt time.Time `json:"created_at"`
}

type Score struct {
    Subject string  `json:"subject"`
    Value   float64 `json:"value"`
}

// 添加成绩
func (s *Student) AddScore(subject string, value float64) {
    s.Scores = append(s.Scores, Score{Subject: subject, Value: value})
}

// 计算平均分
func (s *Student) AverageScore() float64 {
    if len(s.Scores) == 0 {
        return 0
    }
    sum := 0.0
    for _, score := range s.Scores {
        sum += score.Value
    }
    return sum / float64(len(s.Scores))
}

// 转换为JSON
func (s *Student) ToJSON() (string, error) {
    data, err := json.MarshalIndent(s, "", "  ")
    if err != nil {
        return "", err
    }
    return string(data), nil
}

func main() {
    // 创建学生
    student := &Student{
        ID:        1,
        Name:      "张三",
        Grade:     3,
        CreatedAt: time.Now(),
    }

    // 添加成绩
    student.AddScore("数学", 95)
    student.AddScore("语文", 88)
    student.AddScore("英语", 92)

    // 计算并打印平均分
    fmt.Printf("%s的平均分是: %.2f\n", student.Name, student.AverageScore())

    // 转换为JSON并打印
    jsonStr, _ := student.ToJSON()
    fmt.Println("\n学生信息(JSON格式):")
    fmt.Println(jsonStr)
}
```

## 单元测试示例

```
package main

import "testing"

func TestStudent_AverageScore(t *testing.T) {
    student := &Student{
        ID:   1,
        Name: "张三",
    }

    // 测试空成绩的情况
    if avg := student.AverageScore(); avg != 0 {
        t.Errorf("空成绩期望平均分为0，实际得到%f", avg)
    }

    // 测试添加成绩后的平均分
    student.AddScore("数学", 90)
    student.AddScore("语文", 80)

    expected := 85.0
    if avg := student.AverageScore(); avg != expected {
        t.Errorf("期望平均分为%f，实际得到%f", expected, avg)
    }
}
```

### 嵌套结构体的字段名冲突

当嵌套结构体中存在相同的字段名时，为了避免歧义，需要指定具体的内嵌结构体的字段。

```
type Address struct {
    Province   string
    City       string
    CreateTime string
}

type Email struct {
    Account    string
    CreateTime string
}

type User struct {
    Name   string
    Gender string
    Address
    Email
}

func main() {
    var user User
    user.Name = "张三"
    user.Gender = "男"
    // user.CreateTime = "2019" //ambiguous selector user.CreateTime
    user.Address.CreateTime = "2000" //指定Address结构体中的CreateTime
    user.Email.CreateTime = "2000"   //指定Email结构体中的CreateTime
}
```

### 结构体的"继承"

Go 语言中使用结构体也可以实现其他编程语言中面向对象的继承特性：

```
type Animal struct {
    name string
}

func (a *Animal) move() {
    fmt.Printf("%s会动！\n", a.name)
}

type Dog struct {
    Feet    int8
    *Animal //通过嵌套匿名结构体指针实现继承
}

func (d *Dog) bark() {
    fmt.Printf("%s会汪汪汪~\n", d.name)
}

func main() {
    d1 := &Dog{
        Feet: 4,
        Animal: &Animal{ //注意嵌套的是结构体指针
            name: "旺财",
        },
    }
    d1.bark() //旺财会汪汪汪~
    d1.move() //旺财会动！
}
```

在上面的示例中，`Dog` 结构体通过嵌套 `Animal` 匿名结构体指针，实现了对 `Animal` 的"继承"。这使得 `Dog` 不仅可以访问 `Animal` 的字段，还可以直接调用 `Animal` 的方法。

# 练习题

## 练习 1：切片类型的结构体

猜一下下面代码的运行结果：

```
package main

import "fmt"

type student struct {
    id   int
    name string
    age  int
}

func demo(ce []student) {
    //切片是引用传递，是可以改变值的
    ce[1].age = 999
    // ce = append(ce, student{3, "xiaowang", 56})
    // return ce
}

func main() {
    var ce []student  //定义一个切片类型的结构体
    ce = []student{
        student{1, "xiaoming", 22},
        student{2, "xiaozhang", 33},
    }
    fmt.Println(ce)
    demo(ce)
    fmt.Println(ce)
}
```

## 练习 2：删除 map 类型的结构体

```
package main

import "fmt"

type student struct {
    id   int
    name string
    age  int
}

func main() {
    ce := make(map[int]student)
    ce[1] = student{1, "xiaolizi", 22}
    ce[2] = student{2, "wang", 23}
    fmt.Println(ce)
    delete(ce, 2)
    fmt.Println(ce)
}
```

思考：

1. 第一个练习中，为什么切片类型的结构体可以修改其中的值？
2. 第二个练习中，如何删除 map 中的某个元素？
3. 这两个练习说明了 Go 语言中引用类型和值类型的区别，你能总结一下吗？

# 补充知识点

## 结构体指针

在 Go 语言中，我们还可以通过使用结构体指针来操作结构体：

```
type Person struct {
    Name string
    Age  int
}

func main() {
    // 创建结构体指针的几种方式
    var p1 = new(Person)  // 使用new关键字
    p1.Name = "张三"      // Go语言中，结构体指针可以直接使用点号访问成员
    p1.Age = 18

    var p2 = &Person{}    // 取地址操作
    p2.Name = "李四"
    p2.Age = 20

    // 直接初始化结构体指针
    p3 := &Person{
        Name: "王五",
        Age: 25,
    }
}
```

## 构造函数

Go 语言的结构体没有构造函数，我们可以自己实现：

```
type Person struct {
    name string
    age  int
}

// 构造函数命名通常以New开头
func NewPerson(name string, age int) *Person {
    return &Person{
        name: name,
        age:  age,
    }
}

// 当结构体比较大时，使用指针效率更高
func NewPersonValue(name string, age int) Person {
    return Person{
        name: name,
        age:  age,
    }
}
```

## 结构体的内存布局

结构体的内存布局涉及内存对齐：

```
type example struct {
    a bool    // 1字节
    b int16   // 2字节
    c []int   // 24字节
}

func main() {
    var e example
    fmt.Printf("bool占用：%d字节\n", unsafe.Sizeof(e.a))
    fmt.Printf("int16占用：%d字节\n", unsafe.Sizeof(e.b))
    fmt.Printf("切片占用：%d字节\n", unsafe.Sizeof(e.c))
    fmt.Printf("结构体占用：%d字节\n", unsafe.Sizeof(e))
}
```

字段顺序会影响结构体的大小，合理的字段顺序可以减少内存占用。

## 结构体的零值

结构体的零值是每个字段都是对应类型的零值：

```
type Person struct {
    Name string    // 零值为""
    Age  int       // 零值为0
    Married bool   // 零值为false
    Scores []int   // 零值为nil
}

func main() {
    var p Person
    fmt.Printf("%#v\n", p)
    // 输出：main.Person{Name:"", Age:0, Married:false, Scores:[]int(nil)}
}
```

## 结构体的可见性

Go 语言通过大小写控制可见性：

```
type person struct {  // 小写，仅包内可见
    name string      // 小写，仅包内可见
    Age  int        // 大写，可导出
}

type Student struct {  // 大写，可导出
    Name string       // 大写，可导出
    age  int         // 小写，仅包内可见
}
```

跨包使用结构体的最佳实践：

```
// model/person.go
package model

type Person struct {
    Name string
    age  int    // 私有字段
}

// 提供方法来访问私有字段
func (p *Person) GetAge() int {
    return p.age
}

func (p *Person) SetAge(age int) {
    if age > 0 && age < 150 {
        p.age = age
    }
}
```

# 扩展阅读（其他教程）

1. <https://www.topgoer.com/go%E5%9F%BA%E7%A1%80/%E7%BB%93%E6%9E%84%E4%BD%93.html>
2. <https://go.sixue.work/golang/c02/c02_01.html>
3. [通过指针修改数组，和结构体](https://campus.wps.cn/contentpreview/568149b3-c52c-4b06-9faf-1b2747abaa2a)
4. [Go 官方博客：JSON and Go](https://blog.golang.org/json)
5. [Effective Go: Structs](https://golang.org/doc/effective_go.html#structs)
