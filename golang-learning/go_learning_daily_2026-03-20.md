# Go 语言学习日报

> 每天学习时间：30分钟 | 零基础入门 | 推送时间：每天 08:30 (北京时间，UTC 00:30)

---

## 📅 2026-03-20 周四 - 第4天

---

### 🎯 今日目标：掌握 Go 的基础数据结构

---

## 📖 第一部分：基础知识（15分钟）

---

### 1. 数组（Array）

**数组**：固定长度、相同类型的集合

#### 基本语法

```go
var 变量名 [长度]类型 = [长度]类型{值1, 值2, ...}
```

#### 示例1：声明数组

```go
func main() {
    // 方式1：指定长度和值
    var arr1 [3]int = [3]int{1, 2, 3}
    fmt.Println(arr1)
    // 输出：[1 2 3]

    // 方式2：自动推断长度
    arr2 := [...]string{"a", "b", "c"}
    fmt.Println(arr2)
    // 输出：[a b c]

    // 方式3：指定索引的值
    arr3 := [5]int{1: 10, 3: 30}  // arr[1]=10, arr[3]=30
    fmt.Println(arr3)
    // 输出：[0 10 0 30 0]
}
```

#### 示例2：访问和修改数组元素

```go
func main() {
    arr := [5]int{10, 20, 30, 40, 50}

    // 访问元素
    fmt.Println("第一个元素:", arr[0])
    fmt.Println("最后一个元素:", arr[len(arr)-1])
    // 输出：第一个元素: 10
    // 输出：最后一个元素: 50

    // 修改元素
    arr[0] = 100
    fmt.Println("修改后:", arr)
    // 输出：修改后: [100 20 30 40 50]
}
```

#### 示例3：遍历数组

```go
func main() {
    arr := [5]int{10, 20, 30, 40, 50}

    // 方式1：索引遍历
    for i := 0; i < len(arr); i++ {
        fmt.Printf("arr[%d] = %d\n", i, arr[i])
    }

    fmt.Println("---")

    // 方式2：range 遍历（推荐）
    for index, value := range arr {
        fmt.Printf("arr[%d] = %d\n", index, value)
    }

    fmt.Println("---")

    // 方式3：只取值
    for _, value := range arr {
        fmt.Println("值:", value)
    }
}
```

#### 示例4：数组是值类型

```go
func modifyArray(arr [5]int) {
    arr[0] = 999  // 修改的是副本
}

func main() {
    arr := [5]int{1, 2, 3, 4, 5}
    modifyArray(arr)
    fmt.Println("原数组:", arr)
    // 输出：原数组: [1 2 3 4 5]（没有改变）
}
```

---

### 2. 切片（Slice）

**切片**：动态长度的数组，更常用！

#### 基本语法

```go
var 切片名 []类型 = []类型{值1, 值2, ...}
```

#### 示例1：声明切片

```go
func main() {
    // 方式1：直接创建
    slice1 := []int{1, 2, 3, 4, 5}
    fmt.Println(slice1)
    // 输出：[1 2 3 4 5]

    // 方式2：make 创建
    slice2 := make([]int, 3)  // 长度为3，值为 [0 0 0]
    fmt.Println(slice2)
    // 输出：[0 0 0]

    // 方式3：make 创建并指定容量
    slice3 := make([]int, 3, 5)  // 长度3，容量5
    fmt.Println("长度:", len(slice3), "容量:", cap(slice3))
    // 输出：长度: 3 容量: 5
}
```

#### 示例2：从数组创建切片

```go
func main() {
    arr := [10]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}

    // 切片包含元素索引 1-4（不包含5）
    slice := arr[1:5]
    fmt.Println("切片:", slice)
    // 输出：切片: [1 2 3 4]

    // 省略开始索引（从头开始）
    slice2 := arr[:3]
    fmt.Println("arr[:3]:", slice2)
    // 输出：arr[:3]: [0 1 2]

    // 省略结束索引（到末尾）
    slice3 := arr[7:]
    fmt.Println("arr[7:]:", slice3)
    // 输出：arr[7:]: [7 8 9]
}
```

#### 示例3：切片追加元素

```go
func main() {
    slice := []int{1, 2, 3}
    fmt.Println("原切片:", slice)
    // 输出：原切片: [1 2 3]

    // 追加一个元素
    slice = append(slice, 4)
    fmt.Println("追加4后:", slice)
    // 输出：追加4后: [1 2 3 4]

    // 追加多个元素
    slice = append(slice, 5, 6)
    fmt.Println("追加5,6后:", slice)
    // 输出：追加5,6后: [1 2 3 4 5 6]

    // 追加另一个切片
    anotherSlice := []int{7, 8, 9}
    slice = append(slice, anotherSlice...)
    fmt.Println("追加切片后:", slice)
    // 输出：追加切片后: [1 2 3 4 5 6 7 8 9]
}
```

#### 示例4：切片的长度和容量

```go
func main() {
    slice := make([]int, 3, 5)
    fmt.Printf("长度: %d, 容量: %d, 切片: %v\n", len(slice), cap(slice), slice)
    // 输出：长度: 3, 容量: 5, 切片: [0 0 0]

    slice = append(slice, 4)
    fmt.Printf("长度: %d, 容量: %d, 切片: %v\n", len(slice), cap(slice), slice)
    // 输出：长度: 4, 容量: 5, 切片: [0 0 0 4]

    slice = append(slice, 5)
    fmt.Printf("长度: %d, 容量: %d, 切片: %v\n", len(slice), cap(slice), slice)
    // 输出：长度: 5, 容量: 5, 切片: [0 0 0 4 5]

    slice = append(slice, 6)  // 超过容量，自动扩容
    fmt.Printf("长度: %d, 容量: %d, 切片: %v\n", len(slice), cap(slice), slice)
    // 输出：长度: 6, 容量: 10, 切片: [0 0 0 4 5 6]
}
```

#### 示例5：切片复制

```go
func main() {
    slice1 := []int{1, 2, 3}

    // 浅拷贝（共享底层数组）
    slice2 := slice1
    slice2[0] = 999
    fmt.Println("slice1:", slice1)  // 输出：slice1: [999 2 3]
    fmt.Println("slice2:", slice2)  // 输出：slice2: [999 2 3]

    // 深拷贝（独立副本）
    slice3 := make([]int, len(slice1))
    copy(slice3, slice1)
    slice3[0] = 888
    fmt.Println("slice1:", slice1)  // 输出：slice1: [999 2 3]
    fmt.Println("slice3:", slice3)  // 输出：slice3: [888 2 3]
}
```

#### 示例6：切片删除元素

```go
func main() {
    slice := []int{1, 2, 3, 4, 5}
    fmt.Println("原切片:", slice)
    // 输出：原切片: [1 2 3 4 5]

    // 删除索引为2的元素
    index := 2
    slice = append(slice[:index], slice[index+1:]...)
    fmt.Println("删除索引2后:", slice)
    // 输出：删除索引2后: [1 2 4 5]
}
```

---

### 3. 映射（Map）

**映射**：键值对集合（类似 Python 的 dict、Java 的 HashMap）

#### 基本语法

```go
var 映射名 map[键类型]值类型 = make(map[键类型]值类型)
```

#### 示例1：声明和初始化 Map

```go
func main() {
    // 方式1：make 创建
    m1 := make(map[string]int)
    m1["Alice"] = 25
    m1["Bob"] = 30
    fmt.Println("m1:", m1)
    // 输出：m1: map[Alice:25 Bob:30]

    // 方式2：直接初始化
    m2 := map[string]int{
        "Alice": 25,
        "Bob":   30,
        "Charlie": 35,
    }
    fmt.Println("m2:", m2)
    // 输出：m2: map[Alice:25 Bob:30 Charlie:35]
}
```

#### 示例2：访问和修改 Map

```go
func main() {
    scores := map[string]int{
        "Alice": 95,
        "Bob":   88,
    }

    // 访问元素
    fmt.Println("Alice的分数:", scores["Alice"])
    // 输出：Alice的分数: 95

    // 修改元素
    scores["Alice"] = 98
    fmt.Println("修改后:", scores["Alice"])
    // 输出：修改后: 98

    // 添加元素
    scores["Charlie"] = 92
    fmt.Println("添加后:", scores)
    // 输出：添加后: map[Alice:98 Bob:88 Charlie:92]
}
```

#### 示例3：检查键是否存在

```go
func main() {
    scores := map[string]int{
        "Alice": 95,
        "Bob":   88,
    }

    // 方式1：检查键是否存在
    score, exists := scores["Alice"]
    if exists {
        fmt.Println("Alice的分数:", score)
    } else {
        fmt.Println("Alice不存在")
    }

    // 方式2：检查不存在的键
    score2, exists2 := scores["David"]
    if exists2 {
        fmt.Println("David的分数:", score2)
    } else {
        fmt.Println("David不存在")
        // 输出：David不存在
    }
}
```

#### 示例4：删除元素

```go
func main() {
    scores := map[string]int{
        "Alice": 95,
        "Bob":   88,
        "Charlie": 92,
    }
    fmt.Println("删除前:", scores)
    // 输出：删除前: map[Alice:95 Bob:88 Charlie:92]

    // 删除元素
    delete(scores, "Bob")
    fmt.Println("删除Bob后:", scores)
    // 输出：删除Bob后: map[Alice:95 Charlie:92]

    // 删除不存在的键（不会报错）
    delete(scores, "David")
}
```

#### 示例5：遍历 Map

```go
func main() {
    scores := map[string]int{
        "Alice":   95,
        "Bob":     88,
        "Charlie": 92,
    }

    // 遍历 Map（注意：顺序不保证）
    for name, score := range scores {
        fmt.Printf("%s: %d\n", name, score)
    }
    // 输出（顺序可能不同）：
    // Alice: 95
    // Bob: 88
    // Charlie: 92
}
```

#### 示例6：Map 的长度

```go
func main() {
    scores := map[string]int{
        "Alice": 95,
        "Bob":   88,
    }
    fmt.Println("Map长度:", len(scores))
    // 输出：Map长度: 2
}
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：数组逆序

**任务**：将数组元素逆序

```go
func reverseArray(arr [5]int) [5]int {
    n := len(arr)
    for i := 0; i < n/2; i++ {
        arr[i], arr[n-1-i] = arr[n-1-i], arr[i]
    }
    return arr
}

func main() {
    arr := [5]int{1, 2, 3, 4, 5}
    fmt.Println("原数组:", arr)
    // 输出：原数组: [1 2 3 4 5]

    reversed := reverseArray(arr)
    fmt.Println("逆序后:", reversed)
    // 输出：逆序后: [5 4 3 2 1]
}
```

---

### 练习2：切片去重

**任务**：删除切片中的重复元素

```go
func removeDuplicates(slice []int) []int {
    keys := make(map[int]bool)
    result := []int{}

    for _, item := range slice {
        if !keys[item] {
            keys[item] = true
            result = append(result, item)
        }
    }

    return result
}

func main() {
    slice := []int{1, 2, 2, 3, 4, 4, 5, 5, 5}
    fmt.Println("原切片:", slice)
    // 输出：原切片: [1 2 2 3 4 4 5 5 5]

    unique := removeDuplicates(slice)
    fmt.Println("去重后:", unique)
    // 输出：去重后: [1 2 3 4 5]
}
```

---

### 练习3：统计词频

**任务**：统计字符串中每个单词出现的次数

```go
import (
    "fmt"
    "strings"
)

func wordCount(text string) map[string]int {
    words := strings.Fields(text)
    counts := make(map[string]int)

    for _, word := range words {
        counts[word]++
    }

    return counts
}

func main() {
    text := "hello world hello go go world"
    fmt.Println("原文:", text)
    // 输出：原文: hello world hello go go world

    counts := wordCount(text)
    fmt.Println("词频统计:")
    for word, count := range counts {
        fmt.Printf("  %s: %d\n", word, count)
    }
    // 输出：
    // 词频统计:
    //   hello: 2
    //   world: 2
    //   go: 2
}
```

---

### 练习4：找两个数组的交集

**任务**：找出两个数组共有的元素

```go
func intersection(arr1, arr2 []int) []int {
    set := make(map[int]bool)
    result := []int{}

    // 将 arr1 的元素加入 set
    for _, num := range arr1 {
        set[num] = true
    }

    // 检查 arr2 的元素是否在 set 中
    for _, num := range arr2 {
        if set[num] {
            result = append(result, num)
            delete(set, num)  // 避免重复
        }
    }

    return result
}

func main() {
    arr1 := []int{1, 2, 3, 4, 5}
    arr2 := []int{3, 4, 5, 6, 7}
    fmt.Println("数组1:", arr1)
    fmt.Println("数组2:", arr2)
    // 输出：
    // 数组1: [1 2 3 4 5]
    // 数组2: [3 4 5 6 7]

    inter := intersection(arr1, arr2)
    fmt.Println("交集:", inter)
    // 输出：交集: [3 4 5]
}
```

---

### 练习5：实现一个简单的购物车

**任务**：用 Map 实现购物车

```go
import (
    "fmt"
)

type Product struct {
    Name  string
    Price float64
}

type Cart struct {
    Items map[Product]int  // 商品 -> 数量
}

func NewCart() *Cart {
    return &Cart{
        Items: make(map[Product]int),
    }
}

func (c *Cart) Add(product Product, quantity int) {
    c.Items[product] += quantity
}

func (c *Cart) Remove(product Product) {
    delete(c.Items, product)
}

func (c *Cart) Total() float64 {
    total := 0.0
    for product, quantity := range c.Items {
        total += product.Price * float64(quantity)
    }
    return total
}

func (c *Cart) Print() {
    fmt.Println("购物车:")
    for product, quantity := range c.Items {
        fmt.Printf("  %s x%d = %.2f\n", product.Name, quantity, product.Price*float64(quantity))
    }
    fmt.Printf("总计: %.2f\n", c.Total())
}

func main() {
    cart := NewCart()

    apple := Product{Name: "苹果", Price: 5.5}
    banana := Product{Name: "香蕉", Price: 3.2}

    cart.Add(apple, 2)
    cart.Add(banana, 3)
    cart.Add(apple, 1)  // 再加一个苹果

    cart.Print()
    // 输出：
    // 购物车:
    //   苹果 x3 = 16.50
    //   香蕉 x3 = 9.60
    // 总计: 26.10

    cart.Remove(banana)
    fmt.Println("\n删除香蕉后:")
    cart.Print()
    // 输出：
    // 删除香蕉后:
    // 购物车:
    //   苹果 x3 = 16.50
    // 总计: 16.50
}
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| 数组声明和操作 | 🎯 已掌握 |
| 切片基础操作 | 🎯 已掌握 |
| 切片追加和扩容 | 🎯 已掌握 |
| 切片复制和删除 | 🎯 已掌握 |
| Map 声明和操作 | 🎯 已掌握 |
| Map 遍历和删除 | 🎯 已掌握 |

---

## 📝 今天的作业（5分钟）

1. ✅ 完成 5 个练习
2. ✅ 理解切片扩容机制
3. ✅ 练习使用 Map 统计数据

---

## 🚀 下一步预告

```
第1天：Go 语言基础 ✅
第2天：流程控制 ✅
第3天：函数与错误处理 ✅
第4天：数组、切片、映射 ✅
第5天：结构体与方法
第6天：并发基础（Goroutine）
第7天：接口 + 综合项目
```

---

## 📅 明天预告（第5天）

| 时间 | 内容 |
|------|------|
| 15分钟 | **面向对象基础**：指针、结构体（Struct）、方法（Method） |
| 15分钟 | **实战练习**：图书管理系统、学生信息管理 |

**明天你将**：
- ✅ 理解 Go 的指针
- ✅ 掌握结构体
- ✅ 学会定义方法

---

## 💡 常见错误

### 错误1：切片越界

```go
// ❌ 错误
slice := []int{1, 2, 3}
fmt.Println(slice[3])  // 索引从0开始，最大是2

// ✅ 正确
fmt.Println(slice[2])  // 输出：3
```

---

### 错误2：向 nil 切片追加元素

```go
// ✅ 可以向 nil 切片追加
var slice []int  // nil 切片
slice = append(slice, 1)  // 正常工作
```

---

### 错误3：并发访问 Map

```go
// ❌ 错误（多个 goroutine 同时访问 Map 会导致 panic）
// 需要 sync.Map 或加锁

// ✅ 正确（单线程访问）
m := make(map[string]int)
m["key"] = 1
```

---

## 📚 今日总结

**Go 数据结构的特点**：
- ✅ 数组：固定长度，值类型
- ✅ 切片：动态长度，引用类型，非常灵活
- ✅ Map：键值对，高效查找

**今天掌握的技能**：
- ✅ 使用数组存储固定数量数据
- ✅ 使用切片处理动态数据
- ✅ 使用 Map 存储键值对
- ✅ 理解切片的底层机制
- ✅ 解决常见的数据结构问题

---

**学习进度**：📊 4/7 天完成

---

*最后更新：2026-03-20*

**继续加油！明天学习面向对象基础！** 🎉
