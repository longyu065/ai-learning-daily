# Go 语言学习日报

> 每天学习时间：30分钟 | 零基础入门 | 推送时间：每天 08:30 (北京时间，UTC 00:30)

---

## 📅 2026-03-22 周六 - 第6天

---

### 🎯 今日目标：掌握 Go 的并发编程

---

## 📖 第一部分：基础知识（15分钟）

---

### 1. Goroutine

**Goroutine**：轻量级线程，Go 并发的核心

#### 示例1：启动 Goroutine

```go
import (
    "fmt"
    "time"
)

func sayHello() {
    fmt.Println("Hello from goroutine!")
}

func main() {
    // 启动一个 goroutine
    go sayHello()

    fmt.Println("Main function")

    // 等待 goroutine 执行
    time.Sleep(time.Second)
}
```

**输出**：
```
Main function
Hello from goroutine!
```

#### 示例2：多个 Goroutine

```go
func printNumbers(prefix string) {
    for i := 0; i < 5; i++ {
        fmt.Printf("%s: %d\n", prefix, i)
        time.Sleep(100 * time.Millisecond)
    }
}

func main() {
    go printNumbers("Goroutine 1")
    go printNumbers("Goroutine 2")
    printNumbers("Main")

    time.Sleep(time.Second)
}
```

**输出**：
```
Goroutine 1: 0
Goroutine 2: 0
Main: 0
Goroutine 1: 1
Goroutine 2: 1
Main: 1
...
```

#### 示例3：匿名 Goroutine

```go
func main() {
    // 匿名函数 goroutine
    go func(msg string) {
        fmt.Println(msg)
    }("Hello from anonymous goroutine")

    time.Sleep(500 * time.Millisecond)
}
```

---

### 2. Channel

**Channel**：用于 goroutine 之间通信

#### 示例1：创建和使用 Channel

```go
import (
    "fmt"
    "time"
)

func main() {
    // 创建一个字符串 channel
    ch := make(chan string)

    // goroutine 发送数据
    go func() {
        ch <- "Hello, Channel!"  // 发送
    }()

    // 主 goroutine 接收数据
    msg := <-ch  // 接收
    fmt.Println(msg)
    // 输出：Hello, Channel!
}
```

#### 示例2：缓冲 Channel

```go
func main() {
    // 创建缓冲大小为 2 的 channel
    ch := make(chan string, 2)

    // 发送数据（不会阻塞，因为缓冲未满）
    ch <- "First"
    ch <- "Second"

    // 接收数据
    fmt.Println(<-ch)  // First
    fmt.Println(<-ch)  // Second
}
```

#### 示例3：关闭 Channel

```go
func producer(ch chan<- int) {
    for i := 0; i < 5; i++ {
        ch <- i
    }
    close(ch)  // 关闭 channel
}

func consumer(ch <-chan int) {
    for value := range ch {  // range 自动检测 channel 关闭
        fmt.Println("Received:", value)
    }
}

func main() {
    ch := make(chan int)

    go producer(ch)
    consumer(ch)
}
```

#### 示例4：检查 Channel 是否关闭

```go
func main() {
    ch := make(chan int, 2)
    ch <- 1
    ch <- 2
    close(ch)

    // 接收并检查
    for i := 0; i < 3; i++ {
        value, ok := <-ch
        if ok {
            fmt.Println("Received:", value)
        } else {
            fmt.Println("Channel closed")
        }
    }
}
```

**输出**：
```
Received: 1
Received: 2
Channel closed
```

---

### 3. Select

**Select**：同时等待多个 channel

#### 示例1：基本 Select

```go
import (
    "fmt"
    "time"
)

func main() {
    ch1 := make(chan string)
    ch2 := make(chan string)

    go func() {
        time.Sleep(1 * time.Second)
        ch1 <- "from ch1"
    }()

    go func() {
        time.Sleep(2 * time.Second)
        ch2 <- "from ch2"
    }()

    // select 会选择第一个准备好的 channel
    select {
    case msg1 := <-ch1:
        fmt.Println(msg1)
    case msg2 := <-ch2:
        fmt.Println(msg2)
    }
}
```

#### 示例2：超时处理

```go
import (
    "fmt"
    "time"
)

func main() {
    ch := make(chan string)

    go func() {
        time.Sleep(2 * time.Second)
        ch <- "Hello"
    }()

    select {
    case msg := <-ch:
        fmt.Println("Received:", msg)
    case <-time.After(1 * time.Second):
        fmt.Println("Timeout!")
    }
    // 输出：Timeout!
}
```

#### 示例3：default 分支

```go
func main() {
    ch := make(chan string)

    select {
    case msg := <-ch:
        fmt.Println("Received:", msg)
    default:
        fmt.Println("No data available")
    }
    // 输出：No data available
}
```

---

### 4. WaitGroup

**WaitGroup**：等待多个 goroutine 完成

```go
import (
    "fmt"
    "sync"
)

func worker(id int, wg *sync.WaitGroup) {
    defer wg.Done()  // 完成时调用

    fmt.Printf("Worker %d starting\n", id)
    // 模拟工作
    // time.Sleep(time.Second)
    fmt.Printf("Worker %d done\n", id)
}

func main() {
    var wg sync.WaitGroup

    // 启动 3 个 worker
    for i := 1; i <= 3; i++ {
        wg.Add(1)  // 增加计数
        go worker(i, &wg)
    }

    wg.Wait()  // 等待所有 worker 完成
    fmt.Println("All workers done")
}
```

**输出**：
```
Worker 1 starting
Worker 2 starting
Worker 3 starting
Worker 1 done
Worker 2 done
Worker 3 done
All workers done
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：生产者-消费者模式

**任务**：生产者生产数据，消费者处理数据

```go
import (
    "fmt"
    "sync"
)

func producer(id int, ch chan<- int, wg *sync.WaitGroup) {
    defer wg.Done()

    for i := 0; i < 3; i++ {
        item := id*100 + i
        fmt.Printf("Producer %d: produced %d\n", id, item)
        ch <- item
    }
}

func consumer(ch <-chan int, wg *sync.WaitGroup) {
    defer wg.Done()

    for item := range ch {
        fmt.Printf("Consumer: consumed %d\n", item)
    }
}

func main() {
    ch := make(chan int, 5)
    var wg sync.WaitGroup

    // 启动生产者
    for i := 1; i <= 3; i++ {
        wg.Add(1)
        go producer(i, ch, &wg)
    }

    // 启动消费者
    wg.Add(1)
    go consumer(ch, &wg)

    // 等待生产者完成
    wg.Wait()

    // 关闭 channel
    close(ch)

    // 等待消费者完成
    wg.Wait()

    fmt.Println("All done!")
}
```

---

### 练习2：并发下载

**任务**：模拟并发下载多个文件

```go
import (
    "fmt"
    "sync"
    "time"
)

type Download struct {
    URL      string
    Filename string
}

func download(d Download, wg *sync.WaitGroup) {
    defer wg.Done()

    fmt.Printf("开始下载: %s\n", d.Filename)
    // 模拟下载
    time.Sleep(time.Duration(100+d.URL[0]) * time.Millisecond)
    fmt.Printf("下载完成: %s\n", d.Filename)
}

func main() {
    downloads := []Download{
        {URL: "file1.com", Filename: "file1.txt"},
        {URL: "file2.com", Filename: "file2.txt"},
        {URL: "file3.com", Filename: "file3.txt"},
        {URL: "file4.com", Filename: "file4.txt"},
    }

    var wg sync.WaitGroup

    start := time.Now()

    // 并发下载
    for _, d := range downloads {
        wg.Add(1)
        go download(d, &wg)
    }

    wg.Wait()

    fmt.Printf("\n总耗时: %v\n", time.Since(start))
}
```

---

### 练习3：并发求和

**任务**：将数组分成多个部分，并发计算每部分的和

```go
import (
    "fmt"
    "sync"
)

func sum(numbers []int, result chan<- int, wg *sync.WaitGroup) {
    defer wg.Done()

    total := 0
    for _, num := range numbers {
        total += num
    }

    result <- total
}

func main() {
    numbers := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

    // 分成 3 部分
    partSize := 4
    numWorkers := 3

    var wg sync.WaitGroup
    result := make(chan int, numWorkers)

    // 启动 workers
    for i := 0; i < numWorkers; i++ {
        wg.Add(1)
        start := i * partSize
        end := start + partSize
        if end > len(numbers) {
            end = len(numbers)
        }

        go sum(numbers[start:end], result, &wg)
    }

    // 等待所有 workers 完成
    go func() {
        wg.Wait()
        close(result)
    }()

    // 计算总和
    total := 0
    for partialSum := range result {
        total += partialSum
    }

    fmt.Printf("总和: %d\n", total)
    // 输出：总和: 55
}
```

---

### 练习4：并发任务池

**任务**：使用固定数量的 goroutine 处理任务

```go
import (
    "fmt"
    "sync"
    "time"
)

type Task struct {
    ID int
}

func worker(id int, tasks <-chan Task, wg *sync.WaitGroup) {
    defer wg.Done()

    for task := range tasks {
        fmt.Printf("Worker %d: 处理任务 %d\n", id, task.ID)
        time.Sleep(100 * time.Millisecond)
    }
}

func main() {
    // 创建任务
    tasks := make(chan Task, 20)

    // 添加任务
    for i := 1; i <= 10; i++ {
        tasks <- Task{ID: i}
    }
    close(tasks)

    // 启动 3 个 worker
    var wg sync.WaitGroup
    numWorkers := 3

    for i := 1; i <= numWorkers; i++ {
        wg.Add(1)
        go worker(i, tasks, &wg)
    }

    wg.Wait()
    fmt.Println("所有任务完成")
}
```

---

### 练习5：Fan-Out/Fan-In 模式

**任务**：多个 goroutine 处理任务，结果汇总到一个 channel

```go
import (
    "fmt"
    "sync"
    "time"
)

func worker(id int, tasks <-chan int, results chan<- int, wg *sync.WaitGroup) {
    defer wg.Done()

    for task := range tasks {
        result := task * task
        fmt.Printf("Worker %d: %d -> %d\n", id, task, result)
        results <- result
        time.Sleep(50 * time.Millisecond)
    }
}

func main() {
    tasks := make(chan int, 10)
    results := make(chan int, 10)

    // 发送任务
    go func() {
        for i := 1; i <= 10; i++ {
            tasks <- i
        }
        close(tasks)
    }()

    // 启动 workers
    var wg sync.WaitGroup
    for i := 1; i <= 3; i++ {
        wg.Add(1)
        go worker(i, tasks, results, &wg)
    }

    // 等待所有 worker 完成
    go func() {
        wg.Wait()
        close(results)
    }()

    // 收集结果
    total := 0
    for result := range results {
        total += result
    }

    fmt.Printf("\n平方和: %d\n", total)
}
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| Goroutine | 🎯 已掌握 |
| Channel（发送/接收/关闭） | 🎯 已掌握 |
| Select | 🎯 已掌握 |
| WaitGroup | 🎯 已掌握 |
| 生产者-消费者模式 | 🎯 已掌握 |
| 并发任务池 | 🎯 已了解 |

---

## 📝 今天的作业（5分钟）

1. ✅ 完成 5 个练习
2. ✅ 理解 Goroutine 的调度
3. ✅ 练习使用 WaitGroup

---

## 🚀 下一步预告

```
第1天：Go 语言基础 ✅
第2天：流程控制 ✅
第3天：函数与错误处理 ✅
第4天：数组、切片、映射 ✅
第5天：结构体与方法 ✅
第6天：并发编程 ✅
第7天：接口 + 综合项目
```

---

## 📅 明天预告（第7天 - 最后一天！）

| 时间 | 内容 |
|------|------|
| 15分钟 | **进阶与接口**：接口、包管理、Go Modules |
| 15分钟 | **综合实战**：构建完整的 CLI 工具 |

**明天你将**：
- ✅ 掌握 Go 的接口
- ✅ 学会使用 Go Modules 管理依赖
- ✅ 构建一个完整的 Go 项目

---

## 💡 常见错误

### 错误1：死锁

```go
// ❌ 错误：死锁
ch := make(chan int)
ch <- 1  // 阻塞，因为没有接收者

// ✅ 正确：使用缓冲 channel
ch := make(chan int, 1)
ch <- 1
```

---

### 错误2：向关闭的 channel 发送数据

```go
// ❌ 错误：panic
ch := make(chan int)
close(ch)
ch <- 1  // panic: send on closed channel

// ✅ 正确：检查 channel 是否已关闭（不能直接检查）
// 应该由发送者负责关闭，接收者不要发送
```

---

### 错误3：忘记关闭 channel

```go
// ❌ 错误：可能导致 goroutine 泄漏
func producer(ch chan<- int) {
    ch <- 1
    // 忘记 close(ch)
}

// ✅ 正确
func producer(ch chan<- int) {
    ch <- 1
    close(ch)
}
```

---

## 📚 今日总结

**Go 并发的特点**：
- ✅ Goroutine：轻量级线程，成本低
- ✅ Channel：安全通信，避免共享内存
- ✅ Select：同时等待多个 channel
- ✅ WaitGroup：等待多个 goroutine 完成

**今天掌握的技能**：
- ✅ 启动和管理 Goroutine
- ✅ 使用 Channel 通信
- ✅ 使用 Select 实现超时
- ✅ 使用 WaitGroup 同步
- ✅ 实现常见的并发模式

---

**学习进度**：📊 6/7 天完成

---

*最后更新：2026-03-22*

**最后一天明天见！** 🎉
