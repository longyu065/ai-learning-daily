# Go 语言学习日报

> 每天学习时间：30分钟 | 零基础入门 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-17 周一 - 第1天

---

### 🎯 今日目标：从零开始学习 Go 语言

---

## 📖 第一部分：基础知识（15分钟）

---

### 1. Go 语言简介

**Go（Golang）**：Google 于 2009 年发布的开源编程语言

**设计理念**：
- ✅ 简单易学（语法简洁）
- ✅ 高效执行（编译型语言，性能接近 C/C++）
- ✅ 原生并发（Goroutine 轻量级协程）
- ✅ 快速编译（秒级编译）
- ✅ 部署方便（单个可执行文件）

**谁在用 Go**：
- Google（Golang 母公司）
- Docker（容器技术）
- Kubernetes（容器编排）
- Uber、Dropbox、Bilibili

---

### 2. 安装 Go 环境

#### Windows 安装（3分钟）

**步骤**：
1. 访问 https://go.dev/dl/
2. 下载 Windows 版本（如：`go1.22.0.windows-amd64.msi`）
3. 双击安装包，一路"下一步"
4. 打开命令行，验证安装：

```bash
go version
# 输出：go version go1.22.0 windows/amd64

go env GOROOT
# 输出：C:\Program Files\Go

go env GOPATH
# 输出：C:\Users\你的用户名\go
```

**成功标志**：显示 Go 版本号，说明安装成功！

---

### 3. 第一个 Go 程序：Hello World

#### 创建项目目录

```bash
# 在任意位置创建项目目录
mkdir hello-go
cd hello-go
```

#### 编写代码

创建文件 `main.go`：

```go
package main  // 每个 Go 程序都需要一个 main 包

import "fmt"  // 导入格式化输出包

// main 函数是程序的入口
func main() {
    fmt.Println("Hello, Go!")  // 打印输出
}
```

#### 运行程序

**方式1：直接运行**
```bash
go run main.go
```

**方式2：编译后运行**
```bash
go build main.go
.\main.exe  # Windows
```

**输出**：
```
Hello, Go!
```

---

### 4. 变量声明

#### 声明变量的 4 种方式

**方式1：完整声明**
```go
var name string = "Alice"
var age int = 25
var isStudent bool = true

fmt.Println(name, age, isStudent)
// 输出：Alice 25 true
```

**方式2：类型推导（省略类型）**
```go
var name = "Alice"
var age = 25

fmt.Println(name, age)
// 输出：Alice 25
```

**方式3：短变量声明（:= 推荐）**
```go
name := "Alice"
age := 25

fmt.Println(name, age)
// 输出：Alice 25
```

**方式4：批量声明**
```go
var (
    name     string = "Alice"
    age      int    = 25
    isStudent bool   = true
)

fmt.Println(name, age, isStudent)
// 输出：Alice 25 true
```

---

### 5. 基本数据类型

| 类型 | 说明 | 示例 |
|------|------|------|
| **string** | 字符串 | `"Hello"` |
| **int / int64** | 整数 | `100` |
| **float64** | 浮点数 | `3.14` |
| **bool** | 布尔值 | `true` / `false` |

**代码示例**：
```go
func main() {
    // 字符串
    name := "Alice"
    fmt.Printf("name: %s, length: %d\n", name, len(name))
    
    // 整数
    age := 25
    fmt.Printf("age: %d\n", age)
    
    // 浮点数
    price := 99.99
    fmt.Printf("price: %.2f\n", price)
    
    // 布尔值
    isStudent := true
    fmt.Printf("isStudent: %t\n", isStudent)
}
```

**输出**：
```
name: Alice, length: 5
age: 25
price: 99.99
isStudent: true
```

---

### 6. 常量

**关键字**：`const`

**代码示例**：
```go
const (
    Pi       = 3.14159
    MaxCount = 100
    AppName  = "MyApp"
)

func main() {
    fmt.Printf("Pi: %.2f\n", Pi)
    fmt.Printf("MaxCount: %d\n", MaxCount)
}
```

**特点**：
- ✅ 常量在编译时确定，不可修改
- ✅ 常量必须有初始值
- ✅ `iota` 可以用于枚举

**iota 示例**：
```go
const (
    Sunday = iota  // 0
    Monday         // 1
    Tuesday        // 2
    Wednesday      // 3
)

func main() {
    fmt.Println(Monday, Tuesday, Wednesday)
    // 输出：1 2 3
}
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：个人信息打印

**任务**：定义变量存储你的信息并打印

```go
func main() {
    // 定义你的信息
    name := "你的名字"
    age := 25
    city := "北京"
    occupation := "程序员"
    
    // 打印信息
    fmt.Printf("姓名：%s\n", name)
    fmt.Printf("年龄：%d 岁\n", age)
    fmt.Printf("城市：%s\n", city)
    fmt.Printf("职业：%s\n", occupation)
}
```

---

### 练习2：计算器

**任务**：计算两个数的和、差、积、商

```go
func main() {
    a := 20
    b := 5
    
    sum := a + b
    diff := a - b
    product := a * b
    quotient := a / b
    
    fmt.Printf("%d + %d = %d\n", a, b, sum)
    fmt.Printf("%d - %d = %d\n", a, b, diff)
    fmt.Printf("%d * %d = %d\n", a, b, product)
    fmt.Printf("%d / %d = %d\n", a, b, quotient)
}
```

**输出**：
```
20 + 5 = 25
20 - 5 = 15
20 * 5 = 100
20 / 5 = 4
```

---

### 练习3：温度转换

**任务**：摄氏度转华氏度

**公式**：`华氏度 = 摄氏度 × 9/5 + 32`

```go
func main() {
    celsius := 30
    fahrenheit := float64(celsius)*9/5 + 32
    
    fmt.Printf("%d 摄氏度 = %.2f 华氏度\n", celsius, fahrenheit)
}
```

**输出**：
```
30 摄氏度 = 86.00 华氏度
```

---

### 练习4：判断闰年

**任务**：判断某年是否为闰年

**闰年规则**：
- 能被 4 整除但不能被 100 整除
- 或能被 400 整除

```go
func main() {
    year := 2024
    isLeapYear := false
    
    if (year%4 == 0 && year%100 != 0) || (year%400 == 0) {
        isLeapYear = true
    }
    
    if isLeapYear {
        fmt.Printf("%d 是闰年\n", year)
    } else {
        fmt.Printf("%d 不是闰年\n", year)
    }
}
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| Go 语言简介 | 🎯 已了解 |
| 安装 Go 环境 | 🎯 已掌握 |
| Hello World | 🎯 已掌握 |
| 变量声明（4种方式） | 🎯 已掌握 |
| 基本数据类型 | 🎯 已掌握 |
| 常量与 iota | 🎯 已了解 |

---

## 📝 今天的作业（5分钟）

1. ✅ 安装 Go 环境并验证
2. ✅ 编写 Hello World 程序
3. ✅ 完成 4 个练习
4. ✅ 尝试修改练习，熟悉语法

---

## 🚀 下一步预告

```
第1天：Go 语言基础 ✅
第2天：流程控制（if/for/switch）
第3天：函数与错误处理
第4天：数组、切片、映射
第5天：结构体与方法
第6天：并发基础（Goroutine）
第7天：接口 + 综合项目
```

---

## 📅 明天预告（第2天）

| 时间 | 内容 |
|------|------|
| 15分钟 | **基础语法**：if 条件、for 循环、switch 语句 |
| 15分钟 | **实战练习**：猜数字游戏、FizzBuzz、打印九九乘法表 |

**明天你将**：
- ✅ 掌握 Go 的流程控制语句
- ✅ 学会条件判断和循环
- ✅ 完成有趣的小练习

---

## 💡 常见错误

### 错误1：变量声明但未使用
```go
func main() {
    name := "Alice"
    // ❌ 错误：name 声明但未使用
    fmt.Println("Hello")
}
```

**解决**：
```go
func main() {
    name := "Alice"
    fmt.Println("Hello", name)  // ✅ 使用变量
}
```

---

### 错误2：导入未使用的包
```go
import (
    "fmt"
    "strings"  // ❌ 错误：strings 未使用
)

func main() {
    fmt.Println("Hello")
}
```

**解决**：删除未使用的导入

---

## 📚 今日总结

**Go 语言的特点**：
- 🚀 简单易学，语法清晰
- 🔥 编译快速，性能优秀
- 📦 单文件部署，方便分发
- 🛡️ 类型安全，减少错误
- ⚡ 原生并发，适合云原生

**今天掌握的技能**：
- ✅ 安装和配置 Go 环境
- ✅ 编写并运行第一个 Go 程序
- ✅ 声明变量和常量
- ✅ 使用基本数据类型
- ✅ 编写简单的计算程序

---

**学习进度**：📊 1/7 天完成

---

*最后更新：2026-03-17*

**继续加油！明天学习流程控制！** 🎉
