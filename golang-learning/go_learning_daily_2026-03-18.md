# Go 语言学习日报

> 每天学习时间：30分钟 | 零基础入门 | 推送时间：每天 08:30 (北京时间，UTC 00:30)

---

## 📅 2026-03-18 周二 - 第2天

---

### 🎯 今日目标：掌握 Go 的流程控制语句

---

## 📖 第一部分：基础知识（15分钟）

---

### 1. if 条件语句

#### 基本语法

```go
if 条件 {
    // 条件为 true 时执行
} else {
    // 条件为 false 时执行
}
```

#### 示例1：判断数字正负

```go
func main() {
    num := -5

    if num > 0 {
        fmt.Println("正数")
    } else if num < 0 {
        fmt.Println("负数")
    } else {
        fmt.Println("零")
    }

    // 输出：负数
}
```

#### 示例2：if 初始化语句（Go 特有）

```go
func main() {
    // 在 if 中声明变量，作用域仅限于 if-else 块
    if num := rand.Intn(100); num < 50 {
        fmt.Printf("%d 小于 50\n", num)
    } else {
        fmt.Printf("%d 大于等于 50\n", num)
    }
}
```

#### 示例3：逻辑运算符

| 运算符 | 说明 | 示例 |
|--------|------|------|
| `&&` | 逻辑与（AND） | `true && true = true` |
| `\|\|` | 逻辑或（OR） | `true \|\| false = true` |
| `!` | 逻辑非（NOT） | `!true = false` |

```go
func main() {
    age := 25
    hasLicense := true

    // 必须同时满足两个条件
    if age >= 18 && hasLicense {
        fmt.Println("可以开车")
    } else {
        fmt.Println("不能开车")
    }

    // 输出：可以开车
}
```

---

### 2. for 循环

#### Go 只有 for 循环，没有 while

#### 写法1：标准 for 循环（最常用）

```go
for 初始化; 条件; 后置操作 {
    // 循环体
}
```

**示例**：
```go
for i := 0; i < 5; i++ {
    fmt.Println(i)
}
// 输出：0 1 2 3 4
```

#### 写法2：省略初始化和后置（类似 while）

```go
i := 0
for i < 5 {
    fmt.Println(i)
    i++
}
// 输出：0 1 2 3 4
```

#### 写法3：无限循环

```go
for {
    // 无限循环，需要用 break 跳出
    fmt.Println("循环中...")
    // break  // 取消注释可以跳出
}
```

#### 写法4：range 遍历

```go
// 遍历切片
numbers := []int{1, 2, 3, 4, 5}
for index, value := range numbers {
    fmt.Printf("索引: %d, 值: %d\n", index, value)
}
// 输出：
// 索引: 0, 值: 1
// 索引: 1, 值: 2
// ...

// 遍历 map
scores := map[string]int{"Alice": 95, "Bob": 88}
for name, score := range scores {
    fmt.Printf("%s: %d\n", name, score)
}
// 输出：
// Alice: 95
// Bob: 88
```

#### 循环控制：break 和 continue

```go
func main() {
    // break：跳出整个循环
    for i := 0; i < 10; i++ {
        if i == 5 {
            break  // 跳出循环
        }
        fmt.Println(i)
    }
    // 输出：0 1 2 3 4

    fmt.Println("---")

    // continue：跳过本次循环
    for i := 0; i < 5; i++ {
        if i == 2 {
            continue  // 跳过本次
        }
        fmt.Println(i)
    }
    // 输出：0 1 3 4
}
```

---

### 3. switch 语句

#### Go 的 switch 特点：
- ✅ 不需要 `break`（自动 break）
- ✅ 支持 `case` 表达式
- ✅ 可以没有条件（类似 if-else）

#### 示例1：基本用法

```go
func main() {
    day := 3

    switch day {
    case 1:
        fmt.Println("周一")
    case 2:
        fmt.Println("周二")
    case 3:
        fmt.Println("周三")
    default:
        fmt.Println("其他")
    }
    // 输出：周三
}
```

#### 示例2：一个 case 多个值

```go
func main() {
    day := 6

    switch day {
    case 1, 2, 3, 4, 5:
        fmt.Println("工作日")
    case 6, 7:
        fmt.Println("周末")
    }
    // 输出：周末
}
```

#### 示例3：case 表达式

```go
func main() {
    num := 15

    switch {
    case num < 10:
        fmt.Println("个位数")
    case num < 100:
        fmt.Println("两位数")
    default:
        fmt.Println("三位数或更多")
    }
    // 输出：两位数
}
```

#### 示例4：fallthrough（继续执行下一个 case）

```go
func main() {
    score := 85

    switch {
    case score >= 90:
        fmt.Println("优秀")
        fallthrough  // 继续执行下一个 case
    case score >= 60:
        fmt.Println("及格")
        fallthrough
    default:
        fmt.Println("评分完成")
    }
    // 输出：
    // 及格
    // 评分完成
}
```

---

### 4. 嵌套循环

```go
func main() {
    // 打印九九乘法表
    for i := 1; i <= 9; i++ {
        for j := 1; j <= i; j++ {
            fmt.Printf("%d×%d=%-2d ", j, i, i*j)
        }
        fmt.Println()
    }
}
```

**输出**：
```
1×1=1  
1×2=2  2×2=4  
1×3=3  2×3=6  3×3=9  
1×4=4  2×4=8  3×4=12 4×4=16 
1×5=5  2×5=10 3×5=15 4×5=20 5×5=25 
...
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：猜数字游戏

**任务**：随机生成 1-100 的数字，让用户猜

```go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

func main() {
    // 初始化随机数种子
    rand.Seed(time.Now().UnixNano())

    // 生成 1-100 的随机数
    target := rand.Intn(100) + 1

    fmt.Println("猜数字游戏（1-100）")
    fmt.Println("----------------")

    for {
        fmt.Print("请输入你的猜测: ")

        var guess int
        fmt.Scanln(&guess)

        if guess == target {
            fmt.Println("🎉 恭喜你猜对了！")
            break
        } else if guess < target {
            fmt.Println("太小了！")
        } else {
            fmt.Println("太大了！")
        }
    }
}
```

**运行示例**：
```
猜数字游戏（1-100）
----------------
请输入你的猜测: 50
太小了！
请输入你的猜测: 75
太大了！
请输入你的猜测: 65
太小了！
请输入你的猜测: 70
🎉 恭喜你猜对了！
```

---

### 练习2：FizzBuzz 问题

**任务**：输出 1-100，3 的倍数输出 Fizz，5 的倍数输出 Buzz，15 的倍数输出 FizzBuzz

```go
func main() {
    for i := 1; i <= 100; i++ {
        switch {
        case i%15 == 0:  // 15 的倍数
            fmt.Println("FizzBuzz")
        case i%3 == 0:   // 3 的倍数
            fmt.Println("Fizz")
        case i%5 == 0:   // 5 的倍数
            fmt.Println("Buzz")
        default:
            fmt.Println(i)
        }
    }
}
```

**部分输出**：
```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
...
```

---

### 练习3：成绩等级评定

**任务**：输入分数，输出等级

```go
func main() {
    scores := []int{95, 82, 67, 55, 98}

    for _, score := range scores {
        var grade string

        switch {
        case score >= 90:
            grade = "A"
        case score >= 80:
            grade = "B"
        case score >= 70:
            grade = "C"
        case score >= 60:
            grade = "D"
        default:
            grade = "F"
        }

        fmt.Printf("分数: %d, 等级: %s\n", score, grade)
    }
}
```

**输出**：
```
分数: 95, 等级: A
分数: 82, 等级: B
分数: 67, 等级: D
分数: 55, 等级: F
分数: 98, 等级: A
```

---

### 练习4：找最大值

**任务**：找出切片中的最大值

```go
func main() {
    numbers := []int{23, 45, 12, 67, 34, 89, 56}

    max := numbers[0]

    for _, num := range numbers {
        if num > max {
            max = num
        }
    }

    fmt.Printf("最大值: %d\n", max)
    // 输出：最大值: 89
}
```

---

### 练习5：判断素数

**任务**：判断一个数是否为素数

```go
func isPrime(n int) bool {
    if n < 2 {
        return false
    }

    for i := 2; i*i <= n; i++ {
        if n%i == 0 {
            return false
        }
    }

    return true
}

func main() {
    numbers := []int{2, 3, 4, 5, 6, 7, 8, 9, 10, 11}

    for _, num := range numbers {
        if isPrime(num) {
            fmt.Printf("%d 是素数\n", num)
        } else {
            fmt.Printf("%d 不是素数\n", num)
        }
    }
}
```

**输出**：
```
2 是素数
3 是素数
4 不是素数
5 是素数
6 不是素数
7 是素数
8 不是素数
9 不是素数
10 不是素数
11 是素数
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| if 条件语句 | 🎯 已掌握 |
| if 初始化语句 | 🎯 已了解 |
| 逻辑运算符（&&、\|\|、!） | 🎯 已掌握 |
| for 循环（4种写法） | 🎯 已掌握 |
| range 遍历 | 🎯 已掌握 |
| break 和 continue | 🎯 已掌握 |
| switch 语句（4种用法） | 🎯 已掌握 |
| fallthrough | 🎯 已了解 |

---

## 📝 今天的作业（5分钟）

1. ✅ 编写猜数字游戏并测试
2. ✅ 完成 FizzBuzz 练习
3. ✅ 编写九九乘法表程序
4. ✅ 尝试优化找最大值的算法

---

## 🚀 下一步预告

```
第1天：Go 语言基础 ✅
第2天：流程控制（if/for/switch）✅
第3天：函数与错误处理
第4天：数组、切片、映射
第5天：结构体与方法
第6天：并发基础（Goroutine）
第7天：接口 + 综合项目
```

---

## 📅 明天预告（第3天）

| 时间 | 内容 |
|------|------|
| 15分钟 | **基础语法**：函数定义、多返回值、defer、panic/recover |
| 15分钟 | **实战练习**：计算器函数、字符串处理、文件操作 |

**明天你将**：
- ✅ 掌握 Go 的函数定义
- ✅ 学会处理错误
- ✅ 理解 defer 的作用

---

## 💡 常见错误

### 错误1：for 循环后多余的分号

```go
// ❌ 错误
for i := 0; i < 5; i++; {  // 多余的分号
    fmt.Println(i)
}

// ✅ 正确
for i := 0; i < 5; i++ {
    fmt.Println(i)
}
```

---

### 错误2：switch 漏掉 default

```go
// 如果所有 case 都不匹配，需要 default
switch day {
case 1:
    fmt.Println("周一")
case 2:
    fmt.Println("周二")
// 如果 day = 3，什么都不输出
// 建议：添加 default
}
```

---

### 错误3：range 遍历时修改切片

```go
numbers := []int{1, 2, 3}

for _, num := range numbers {
    num = num * 2  // ❌ 不会修改原切片
}

fmt.Println(numbers)  // 输出：[1 2 3]
```

**正确做法**：
```go
for i := range numbers {
    numbers[i] *= 2  // ✅ 通过索引修改
}

fmt.Println(numbers)  // 输出：[2 4 6]
```

---

## 📚 今日总结

**Go 流程控制的特点**：
- ✅ 只有 for 循环，没有 while
- ✅ switch 自动 break，更安全
- ✅ if 支持初始化语句，代码更简洁
- ✅ range 遍历切片和 map 很方便

**今天掌握的技能**：
- ✅ 使用 if 条件判断
- ✅ 编写各种 for 循环
- ✅ 使用 switch 语句
- ✅ 控制循环流程（break/continue）
- ✅ 解决常见的编程问题

---

**学习进度**：📊 2/7 天完成

---

*最后更新：2026-03-18*

**继续加油！明天学习函数与错误处理！** 🎉
