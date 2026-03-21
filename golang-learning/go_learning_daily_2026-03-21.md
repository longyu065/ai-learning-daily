# Go 语言学习日报

> 每天学习时间：30分钟 | 零基础入门 | 推送时间：每天 08:30 (北京时间，UTC 00:30)

---

## 📅 2026-03-21 周五 - 第5天

---

### 🎯 今日目标：掌握 Go 的面向对象基础

---

## 📖 第一部分：基础知识（15分钟）

---

### 1. 指针（Pointer）

**指针**：存储变量的内存地址

#### 基本语法

```go
var 指针变量 *类型 = &变量  // & 取地址，* 指针类型
```

#### 示例1：声明和使用指针

```go
func main() {
    num := 42
    fmt.Printf("num 的值: %d, 地址: %p\n", num, &num)
    // 输出：num 的值: 42, 地址: 0xc0000a2000

    // 声明指针
    var ptr *int = &num
    fmt.Printf("ptr 指向的值: %d, ptr 的地址: %p\n", *ptr, ptr)
    // 输出：ptr 指向的值: 42, ptr 的地址: 0xc0000a2000

    // 通过指针修改值
    *ptr = 100
    fmt.Printf("修改后 num: %d\n", num)
    // 输出：修改后 num: 100
}
```

#### 示例2：指针 vs 值传递

```go
// 值传递（不修改原变量）
func modifyValue(x int) {
    x = 999
}

// 指针传递（修改原变量）
func modifyPointer(x *int) {
    *x = 999
}

func main() {
    num := 10

    modifyValue(num)
    fmt.Println("值传递后:", num)  // 输出：值传递后: 10（没变）

    modifyPointer(&num)
    fmt.Println("指针传递后:", num)  // 输出：指针传递后: 999（变了）
}
```

---

### 2. 结构体（Struct）

**结构体**：自定义数据类型，包含多个字段

#### 基本语法

```go
type 结构体名 struct {
    字段1 类型1
    字段2 类型2
    ...
}
```

#### 示例1：定义和初始化结构体

```go
// 定义结构体
type Person struct {
    Name string
    Age  int
    City string
}

func main() {
    // 方式1：按顺序初始化
    p1 := Person{"Alice", 25, "北京"}
    fmt.Println(p1)
    // 输出：{Alice 25 北京}

    // 方式2：指定字段名（推荐）
    p2 := Person{Name: "Bob", Age: 30, City: "上海"}
    fmt.Println(p2)
    // 输出：{Bob 30 上海}

    // 方式3：部分字段初始化
    p3 := Person{Name: "Charlie"}
    fmt.Println(p3)
    // 输出：{Charlie 0 }（未初始化的字段为零值）
}
```

#### 示例2：访问和修改结构体字段

```go
type Rectangle struct {
    Width  float64
    Height float64
}

func main() {
    rect := Rectangle{Width: 10, Height: 5}

    // 访问字段
    fmt.Printf("宽: %.2f, 高: %.2f\n", rect.Width, rect.Height)
    // 输出：宽: 10.00, 高: 5.00

    // 修改字段
    rect.Width = 15
    fmt.Printf("修改后宽: %.2f\n", rect.Width)
    // 输出：修改后宽: 15.00
}
```

#### 示例3：结构体指针

```go
type Person struct {
    Name string
    Age  int
}

// 值传递
func modifyValue(p Person) {
    p.Name = "New Name"  // 修改的是副本
}

// 指针传递
func modifyPointer(p *Person) {
    p.Name = "New Name"  // 修改的是原对象
}

func main() {
    p := Person{Name: "Alice", Age: 25}

    modifyValue(p)
    fmt.Println("值传递后:", p.Name)
    // 输出：值传递后: Alice（没变）

    modifyPointer(&p)
    fmt.Println("指针传递后:", p.Name)
    // 输出：指针传递后: New Name（变了）
}
```

#### 示例4：嵌套结构体

```go
type Address struct {
    City    string
    Street  string
    ZipCode string
}

type Person struct {
    Name    string
    Age     int
    Address Address  // 嵌套结构体
}

func main() {
    p := Person{
        Name: "Alice",
        Age:  25,
        Address: Address{
            City:    "北京",
            Street:  "长安街",
            ZipCode: "100000",
        },
    }

    fmt.Printf("%s 住在 %s %s\n", p.Name, p.Address.City, p.Address.Street)
    // 输出：Alice 住在 北京 长安街
}
```

#### 示例5：结构体方法

**方法**：绑定到结构体的函数

```go
type Rectangle struct {
    Width  float64
    Height float64
}

// 值接收者方法
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// 指针接收者方法（可以修改结构体）
func (r *Rectangle) Resize(width, height float64) {
    r.Width = width
    r.Height = height
}

func main() {
    rect := Rectangle{Width: 10, Height: 5}

    // 调用方法
    fmt.Printf("面积: %.2f\n", rect.Area())
    // 输出：面积: 50.00

    // 调用修改方法
    rect.Resize(15, 8)
    fmt.Printf("修改后面积: %.2f\n", rect.Area())
    // 输出：修改后面积: 120.00
}
```

---

### 3. 方法（Method）

#### 值接收者 vs 指针接收者

| 类型 | 用途 | 示例 |
|------|------|------|
| **值接收者** | 只读操作，不修改结构体 | `func (r Rectangle) Area()` |
| **指针接收者** | 需要修改结构体 | `func (r *Rectangle) Resize()` |

#### 示例：比较两种接收者

```go
type Counter struct {
    count int
}

// 值接收者（不修改）
func (c Counter) Get() int {
    return c.count
}

// 指针接收者（修改）
func (c *Counter) Increment() {
    c.count++
}

func main() {
    c := Counter{count: 0}

    fmt.Println("当前计数:", c.Get())  // 输出：0

    c.Increment()
    fmt.Println("增加后:", c.Get())     // 输出：1

    c.Increment()
    c.Increment()
    fmt.Println("最终:", c.Get())       // 输出：3
}
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：图书管理系统

**任务**：用结构体管理图书

```go
package main

import "fmt"

// 图书结构体
type Book struct {
    Title   string
    Author  string
    Price   float64
    IsBorrowed bool
}

// 图书馆结构体
type Library struct {
    Name  string
    Books []Book
}

// 借书
func (l *Library) BorrowBook(title string) bool {
    for i := range l.Books {
        if l.Books[i].Title == title && !l.Books[i].IsBorrowed {
            l.Books[i].IsBorrowed = true
            fmt.Printf("成功借出: %s\n", title)
            return true
        }
    }
    fmt.Printf("未找到或已借出: %s\n", title)
    return false
}

// 还书
func (l *Library) ReturnBook(title string) {
    for i := range l.Books {
        if l.Books[i].Title == title {
            l.Books[i].IsBorrowed = false
            fmt.Printf("成功归还: %s\n", title)
            return
        }
    }
    fmt.Printf("未找到: %s\n", title)
}

// 查看所有图书
func (l Library) ListBooks() {
    fmt.Printf("\n【%s 图书馆】\n", l.Name)
    fmt.Println("--------------------------------")
    for _, book := range l.Books {
        status := "可借"
        if book.IsBorrowed {
            status = "已借出"
        }
        fmt.Printf("%-20s %-10s %.2f元  %s\n", book.Title, book.Author, book.Price, status)
    }
    fmt.Println("--------------------------------")
}

func main() {
    library := Library{
        Name: "中央图书馆",
        Books: []Book{
            {Title: "Go 语言圣经", Author: "Alan", Price: 89.00, IsBorrowed: false},
            {Title: "深入理解计算机系统", Author: "Bryant", Price: 139.00, IsBorrowed: false},
            {Title: "算法导论", Author: "Cormen", Price: 128.00, IsBorrowed: false},
        },
    }

    // 查看所有图书
    library.ListBooks()

    // 借书
    library.BorrowBook("Go 语言圣经")
    library.BorrowBook("算法导论")

    // 再次查看
    library.ListBooks()

    // 还书
    library.ReturnBook("Go 语言圣经")

    // 再次查看
    library.ListBooks()
}
```

**输出**：
```
【中央图书馆 图书馆】
--------------------------------
Go 语言圣经          Alan       89.00元  可借
深入理解计算机系统    Bryant     139.00元 可借
算法导论            Cormen     128.00元 可借
--------------------------------
成功借出: Go 语言圣经
成功借出: 算法导论

【中央图书馆 图书馆】
--------------------------------
Go 语言圣经          Alan       89.00元  已借出
深入理解计算机系统    Bryant     139.00元 可借
算法导论            Cormen     128.00元 已借出
--------------------------------
成功归还: Go 语言圣经

【中央图书馆 图书馆】
--------------------------------
Go 语言圣经          Alan       89.00元  可借
深入理解计算机系统    Bryant     139.00元 可借
算法导论            Cormen     128.00元 已借出
--------------------------------
```

---

### 练习2：学生信息管理

**任务**：管理学生成绩

```go
type Student struct {
    ID     int
    Name   string
    Scores map[string]float64  // 科目 -> 分数
}

// 添加分数
func (s *Student) AddScore(subject string, score float64) {
    if s.Scores == nil {
        s.Scores = make(map[string]float64)
    }
    s.Scores[subject] = score
}

// 计算平均分
func (s Student) AverageScore() float64 {
    if len(s.Scores) == 0 {
        return 0
    }

    total := 0.0
    for _, score := range s.Scores {
        total += score
    }
    return total / float64(len(s.Scores))
}

// 打印信息
func (s Student) PrintInfo() {
    fmt.Printf("学号: %d, 姓名: %s\n", s.ID, s.Name)
    fmt.Println("成绩:")
    for subject, score := range s.Scores {
        fmt.Printf("  %s: %.1f\n", subject, score)
    }
    fmt.Printf("平均分: %.1f\n", s.AverageScore())
}

func main() {
    students := []Student{
        {ID: 1001, Name: "Alice"},
        {ID: 1002, Name: "Bob"},
        {ID: 1003, Name: "Charlie"},
    }

    // 添加分数
    students[0].AddScore("数学", 95.5)
    students[0].AddScore("英语", 88.0)
    students[0].AddScore("物理", 92.5)

    students[1].AddScore("数学", 78.0)
    students[1].AddScore("英语", 85.5)

    students[2].AddScore("数学", 89.0)
    students[2].AddScore("英语", 92.0)
    students[2].AddScore("物理", 87.5)

    // 打印信息
    for _, student := range students {
        student.PrintInfo()
        fmt.Println()
    }
}
```

---

### 练习3：银行账户

**任务**：模拟银行账户

```go
type Account struct {
    Owner   string
    Balance float64
}

// 存款
func (a *Account) Deposit(amount float64) bool {
    if amount <= 0 {
        fmt.Println("存款金额必须大于0")
        return false
    }
    a.Balance += amount
    fmt.Printf("成功存入 %.2f 元，当前余额: %.2f 元\n", amount, a.Balance)
    return true
}

// 取款
func (a *Account) Withdraw(amount float64) bool {
    if amount <= 0 {
        fmt.Println("取款金额必须大于0")
        return false
    }
    if a.Balance < amount {
        fmt.Println("余额不足")
        return false
    }
    a.Balance -= amount
    fmt.Printf("成功取出 %.2f 元，当前余额: %.2f 元\n", amount, a.Balance)
    return true
}

// 查询余额
func (a Account) QueryBalance() {
    fmt.Printf("账户: %s, 余额: %.2f 元\n", a.Owner, a.Balance)
}

func main() {
    account := Account{
        Owner:   "Alice",
        Balance: 1000.00,
    }

    account.QueryBalance()
    // 输出：账户: Alice, 余额: 1000.00 元

    account.Deposit(500.00)
    // 输出：成功存入 500.00 元，当前余额: 1500.00 元

    account.Withdraw(200.00)
    // 输出：成功取出 200.00 元，当前余额: 1300.00 元

    account.Withdraw(2000.00)
    // 输出：余额不足

    account.QueryBalance()
    // 输出：账户: Alice, 余额: 1300.00 元
}
```

---

### 练习4：二维向量

**任务**：实现二维向量运算

```go
import (
    "fmt"
    "math"
)

type Vector struct {
    X, Y float64
}

// 向量加法
func (v Vector) Add(other Vector) Vector {
    return Vector{v.X + other.X, v.Y + other.Y}
}

// 向量减法
func (v Vector) Sub(other Vector) Vector {
    return Vector{v.X - other.X, v.Y - other.Y}
}

// 向量乘法（标量）
func (v Vector) Mul(scalar float64) Vector {
    return Vector{v.X * scalar, v.Y * scalar}
}

// 向量模长
func (v Vector) Length() float64 {
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

// 点积
func (v Vector) Dot(other Vector) float64 {
    return v.X*other.X + v.Y*other.Y
}

// 字符串表示
func (v Vector) String() string {
    return fmt.Sprintf("(%.2f, %.2f)", v.X, v.Y)
}

func main() {
    v1 := Vector{X: 3, Y: 4}
    v2 := Vector{X: 1, Y: 2}

    fmt.Printf("v1 = %s, v2 = %s\n", v1, v2)
    // 输出：v1 = (3.00, 4.00), v2 = (1.00, 2.00)

    fmt.Printf("v1 + v2 = %s\n", v1.Add(v2))
    // 输出：v1 + v2 = (4.00, 6.00)

    fmt.Printf("v1 - v2 = %s\n", v1.Sub(v2))
    // 输出：v1 - v2 = (2.00, 2.00)

    fmt.Printf("v1 * 2 = %s\n", v1.Mul(2))
    // 输出：v1 * 2 = (6.00, 8.00)

    fmt.Printf("v1 的模长 = %.2f\n", v1.Length())
    // 输出：v1 的模长 = 5.00

    fmt.Printf("v1 · v2 = %.2f\n", v1.Dot(v2))
    // 输出：v1 · v2 = 11.00
}
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| 指针（& 和 *） | 🎯 已掌握 |
| 值传递 vs 指针传递 | 🎯 已掌握 |
| 结构体定义和初始化 | 🎯 已掌握 |
| 结构体嵌套 | 🎯 已掌握 |
| 结构体方法 | 🎯 已掌握 |
| 值接收者 vs 指针接收者 | 🎯 已掌握 |

---

## 📝 今天的作业（5分钟）

1. ✅ 完成 4 个练习
2. ✅ 理解指针和值传递的区别
3. ✅ 练习使用结构体方法

---

## 🚀 下一步预告

```
第1天：Go 语言基础 ✅
第2天：流程控制 ✅
第3天：函数与错误处理 ✅
第4天：数组、切片、映射 ✅
第5天：结构体与方法 ✅
第6天：并发编程（Goroutine）
第7天：接口 + 综合项目
```

---

## 📅 明天预告（第6天）

| 时间 | 内容 |
|------|------|
| 15分钟 | **并发编程**：Goroutine、Channel、Select |
| 15分钟 | **实战练习**：生产者消费者、并发下载 |

**明天你将**：
- ✅ 掌握 Go 的并发模型
- ✅ 使用 Goroutine 实现并发
- ✅ 用 Channel 进行通信

---

## 💡 常见错误

### 错误1：空指针解引用

```go
// ❌ 错误
var ptr *int
fmt.Println(*ptr)  // panic: nil pointer dereference

// ✅ 正确
num := 10
ptr = &num
fmt.Println(*ptr)
```

---

### 错误2：值接收者修改无效

```go
type Counter struct {
    count int
}

// ❌ 错误（值接收者，修改无效）
func (c Counter) Increment() {
    c.count++
}

// ✅ 正确（指针接收者）
func (c *Counter) Increment() {
    c.count++
}
```

---

## 📚 今日总结

**Go 面向对象的特点**：
- ✅ 没有类，用结构体代替
- ✅ 没有继承，用组合代替
- ✅ 方法绑定到结构体
- ✅ 值接收者 vs 指针接收者

**今天掌握的技能**：
- ✅ 使用指针修改原变量
- ✅ 定义和使用结构体
- ✅ 编写结构体方法
- ✅ 理解嵌套结构体
- ✅ 实现实用的数据模型

---

**学习进度**：📊 5/7 天完成

---

*最后更新：2026-03-21*

**继续加油！明天学习并发编程！** 🎉
