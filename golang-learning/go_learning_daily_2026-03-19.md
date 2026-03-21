# Go 语言学习日报

> 每天学习时间：30分钟 | 零基础入门 | 推送时间：每天 08:30 (北京时间，UTC 00:30)

---

## 📅 2026-03-19 周三 - 第3天

---

### 🎯 今日目标：掌握 Go 的函数和错误处理

---

## 📖 第一部分：基础知识（15分钟）

---

### 1. 函数定义

#### 基本语法

```go
func 函数名(参数列表) 返回值类型 {
    // 函数体
    return 返回值
}
```

#### 示例1：无参数、无返回值

```go
func sayHello() {
    fmt.Println("Hello, Go!")
}

func main() {
    sayHello()
    // 输出：Hello, Go!
}
```

#### 示例2：带参数

```go
func greet(name string) {
    fmt.Printf("Hello, %s!\n", name)
}

func main() {
    greet("Alice")
    greet("Bob")
    // 输出：
    // Hello, Alice!
    // Hello, Bob!
}
```

#### 示例3：带返回值

```go
func add(a int, b int) int {
    return a + b
}

func main() {
    result := add(3, 5)
    fmt.Println(result)
    // 输出：8
}
```

#### 示例4：简化参数类型

```go
// 当参数类型相同时，可以简写
func add(a, b int) int {
    return a + b
}
```

---

### 2. 多返回值（Go 的特色）

Go 的函数可以返回多个值，非常常用！

#### 示例1：返回多个值

```go
func divide(a, b int) (int, int) {
    quotient := a / b
    remainder := a % b
    return quotient, remainder
}

func main() {
    q, r := divide(10, 3)
    fmt.Printf("商: %d, 余数: %d\n", q, r)
    // 输出：商: 3, 余数: 1
}
```

#### 示例2：命名返回值

```go
// 返回值可以直接命名
func getMinMax(numbers []int) (min, max int) {
    min = numbers[0]
    max = numbers[0]

    for _, num := range numbers {
        if num < min {
            min = num
        }
        if num > max {
            max = num
        }
    }

    return  // 直接 return，返回命名的变量
}

func main() {
    numbers := []int{5, 2, 8, 1, 9}
    min, max := getMinMax(numbers)
    fmt.Printf("最小值: %d, 最大值: %d\n", min, max)
    // 输出：最小值: 1, 最大值: 9
}
```

#### 示例3：忽略某个返回值

```go
func getUser(id int) (string, int, bool) {
    return "Alice", 25, true
}

func main() {
    name, _, _ := getUser(1)  // 用 _ 忽略不需要的返回值
    fmt.Println(name)
    // 输出：Alice
}
```

---

### 3. 可变参数

```go
// ... 表示可变参数
func sum(numbers ...int) int {
    total := 0
    for _, num := range numbers {
        total += num
    }
    return total
}

func main() {
    fmt.Println(sum(1, 2, 3))          // 输出：6
    fmt.Println(sum(1, 2, 3, 4, 5))      // 输出：15
    fmt.Println(sum())                   // 输出：0
}
```

---

### 4. defer 延迟执行

**defer**：函数返回前执行，常用于资源清理

#### 示例1：基本用法

```go
func main() {
    defer fmt.Println("最后执行")
    fmt.Println("第一个输出")
    fmt.Println("第二个输出")
}
// 输出：
// 第一个输出
// 第二个输出
// 最后执行（defer）
```

#### 示例2：多个 defer（栈结构）

```go
func main() {
    defer fmt.Println("defer 1")
    defer fmt.Println("defer 2")
    defer fmt.Println("defer 3")
    fmt.Println("正常执行")
}
// 输出：
// 正常执行
// defer 3（后进先出）
// defer 2
// defer 1
```

#### 示例3：defer 在函数中的应用

```go
func readFile(filename string) {
    file, err := os.Open(filename)
    if err != nil {
        fmt.Println("打开文件失败")
        return
    }
    defer file.Close()  // 函数返回前自动关闭文件

    fmt.Printf("文件 %s 已打开\n", filename)
}

func main() {
    readFile("test.txt")
}
```

#### 示例4：defer 修改变量

```go
func main() {
    num := 10
    defer fmt.Println("defer 中的 num:", num)
    num = 20
    fmt.Println("修改后的 num:", num)
}
// 输出：
// 修改后的 num: 20
// defer 中的 num: 10（defer 捕获的是定义时的值）
```

---

### 5. 错误处理

Go 没有异常机制，使用返回值来处理错误

#### 示例1：基本错误处理

```go
func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("除数不能为0")
    }
    return a / b, nil
}

func main() {
    result, err := divide(10, 0)
    if err != nil {
        fmt.Println("错误:", err)
        return
    }
    fmt.Println("结果:", result)
    // 输出：错误: 除数不能为0
}
```

#### 示例2：自定义错误

```go
// 自定义错误类型
type MyError struct {
    Code    int
    Message string
}

func (e *MyError) Error() string {
    return fmt.Sprintf("错误 %d: %s", e.Code, e.Message)
}

func validateAge(age int) error {
    if age < 0 {
        return &MyError{Code: 1001, Message: "年龄不能为负数"}
    }
    if age > 150 {
        return &MyError{Code: 1002, Message: "年龄超过合理范围"}
    }
    return nil
}

func main() {
    err := validateAge(-5)
    if err != nil {
        fmt.Println(err)
        // 输出：错误 1001: 年龄不能为负数
    }
}
```

#### 示例3：fmt.Errorf 格式化错误

```go
func getUser(id int) (string, error) {
    if id <= 0 {
        return "", fmt.Errorf("无效的用户 ID: %d", id)
    }
    return "Alice", nil
}

func main() {
    _, err := getUser(-1)
    if err != nil {
        fmt.Println(err)
        // 输出：无效的用户 ID: -1
    }
}
```

---

### 6. panic 和 recover

**panic**：程序崩溃（类似 throw exception）  
**recover**：捕获 panic（类似 catch）

#### 示例1：panic

```go
func main() {
    fmt.Println("开始")
    panic("程序崩溃了！")
    fmt.Println("这行不会执行")
}
// 输出：
// 开始
// panic: 程序崩溃了！
// goroutine 1 [running]:
// ...
```

#### 示例2：recover 捕获 panic

```go
func safeDivision(a, b int) (result int) {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("捕获到 panic:", r)
            result = 0  // 设置默认返回值
        }
    }()

    return a / b
}

func main() {
    result := safeDivision(10, 0)
    fmt.Println("结果:", result)
    // 输出：
    // 捕获到 panic: runtime error: integer divide by zero
    // 结果: 0
}
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：计算器函数

**任务**：实现加减乘除函数

```go
package main

import (
    "errors"
    "fmt"
)

func add(a, b int) int {
    return a + b
}

func subtract(a, b int) int {
    return a - b
}

func multiply(a, b int) int {
    return a * b
}

func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("除数不能为0")
    }
    return a / b, nil
}

func main() {
    fmt.Println("10 + 5 =", add(10, 5))
    fmt.Println("10 - 5 =", subtract(10, 5))
    fmt.Println("10 * 5 =", multiply(10, 5))

    result, err := divide(10, 0)
    if err != nil {
        fmt.Println("除法错误:", err)
    } else {
        fmt.Println("10 / 0 =", result)
    }
}
```

---

### 练习2：统计字符串信息

**任务**：统计字符串的字母数、数字数、空格数

```go
import (
    "fmt"
    "unicode"
)

func countString(s string) (letters, digits, spaces int) {
    for _, char := range s {
        switch {
        case unicode.IsLetter(char):
            letters++
        case unicode.IsDigit(char):
            digits++
        case unicode.IsSpace(char):
            spaces++
        }
    }
    return
}

func main() {
    text := "Hello 123 World 456"
    letters, digits, spaces := countString(text)
    fmt.Printf("字母: %d, 数字: %d, 空格: %d\n", letters, digits, spaces)
    // 输出：字母: 10, 数字: 6, 空格: 3
}
```

---

### 练习3：斐波那契数列

**任务**：计算斐波那契数列的第 n 项

```go
func fibonacci(n int) int {
    if n <= 1 {
        return n
    }
    return fibonacci(n-1) + fibonacci(n-2)
}

func main() {
    for i := 0; i < 10; i++ {
        fmt.Printf("fib(%d) = %d\n", i, fibonacci(i))
    }
    // 输出：
    // fib(0) = 0
    // fib(1) = 1
    // fib(2) = 1
    // fib(3) = 2
    // fib(4) = 3
    // fib(5) = 5
    // fib(6) = 8
    // fib(7) = 13
    // fib(8) = 21
    // fib(9) = 34
}
```

---

### 练习4：阶乘计算

**任务**：计算 n 的阶乘（使用递归和循环两种方式）

```go
// 递归方式
func factorialRecursive(n int) int {
    if n <= 1 {
        return 1
    }
    return n * factorialRecursive(n-1)
}

// 循环方式
func factorialIterative(n int) int {
    result := 1
    for i := 2; i <= n; i++ {
        result *= i
    }
    return result
}

func main() {
    n := 5
    fmt.Printf("%d! (递归) = %d\n", n, factorialRecursive(n))
    fmt.Printf("%d! (循环) = %d\n", n, factorialIterative(n))
    // 输出：
    // 5! (递归) = 120
    // 5! (循环) = 120
}
```

---

### 练习5：验证输入

**任务**：创建一个验证函数，检查用户输入

```go
func validateInput(username, password string) error {
    if len(username) < 3 {
        return fmt.Errorf("用户名至少3个字符")
    }
    if len(password) < 6 {
        return fmt.Errorf("密码至少6个字符")
    }
    return nil
}

func main() {
    // 测试用例
    testCases := []struct {
        username string
        password string
    }{
        {"ab", "123456"},       // 用户名太短
        {"alice", "123"},        // 密码太短
        {"alice", "password"},  // 有效
    }

    for _, tc := range testCases {
        fmt.Printf("测试: username=%s, password=%s\n", tc.username, tc.password)
        err := validateInput(tc.username, tc.password)
        if err != nil {
            fmt.Println("  ❌", err)
        } else {
            fmt.Println("  ✅ 验证通过")
        }
        fmt.Println()
    }
}
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| 函数定义 | 🎯 已掌握 |
| 多返回值 | 🎯 已掌握 |
| 命名返回值 | 🎯 已了解 |
| 可变参数 | 🎯 已掌握 |
| defer 延迟执行 | 🎯 已掌握 |
| 错误处理（error） | 🎯 已掌握 |
| 自定义错误 | 🎯 已了解 |
| panic 和 recover | 🎯 已了解 |

---

## 📝 今天的作业（5分钟）

1. ✅ 完成 5 个练习
2. ✅ 尝试修改斐波那契数列，改用循环实现
3. ✅ 练习使用 defer 关闭资源

---

## 🚀 下一步预告

```
第1天：Go 语言基础 ✅
第2天：流程控制 ✅
第3天：函数与错误处理 ✅
第4天：数组、切片、映射
第5天：结构体与方法
第6天：并发基础（Goroutine）
第7天：接口 + 综合项目
```

---

## 📅 明天预告（第4天）

| 时间 | 内容 |
|------|------|
| 15分钟 | **数据结构**：数组、切片（Slice）、映射（Map） |
| 15分钟 | **实战练习**：数组操作、切片增删、映射统计 |

**明天你将**：
- ✅ 掌握 Go 的基础数据结构
- ✅ 学会使用切片的动态特性
- ✅ 理解映射的键值对存储

---

## 💡 常见错误

### 错误1：函数参数类型不匹配

```go
// ❌ 错误
func add(a, b int) int {
    return a + b
}

add(1, 2.5)  // 2.5 是 float64，不是 int

// ✅ 正确
add(1, 2)
```

---

### 错误2：忽略错误

```go
// ❌ 错误（不推荐）
result, _ := divide(10, 0)

// ✅ 正确（处理错误）
result, err := divide(10, 0)
if err != nil {
    // 处理错误
}
```

---

### 错误3：defer 中使用循环变量

```go
// ❌ 错误
func main() {
    for i := 0; i < 3; i++ {
        defer fmt.Println(i)
    }
    // 输出：2 2 2（因为 defer 延迟执行）
}

// ✅ 正确
func main() {
    for i := 0; i < 3; i++ {
        func(n int) {
            defer fmt.Println(n)
        }(i)
    }
    // 输出：0 1 2
}
```

---

## 📚 今日总结

**Go 函数的特点**：
- ✅ 多返回值：简洁地返回多个值
- ✅ 命名返回值：代码更清晰
- ✅ defer：自动资源清理
- ✅ 错误处理：显式处理，避免隐藏错误
- ✅ panic/recover：处理异常情况

**今天掌握的技能**：
- ✅ 定义和调用函数
- ✅ 使用多返回值
- ✅ 处理错误
- ✅ 使用 defer 清理资源
- ✅ 编写递归函数

---

**学习进度**：📊 3/7 天完成

---

*最后更新：2026-03-19*

**继续加油！明天学习数据结构！** 🎉
