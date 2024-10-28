# ♾️atomic原子操作
1. 多个协程对一个共享计数器进行加减操作
```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

func main() {
	var counter int64
	var wg sync.WaitGroup
	numGoroutines := 10

	wg.Add(numGoroutines)
	for i := 0; i < numGoroutines; i++ {
		go func() {
			defer wg.Done()
			for j := 0; j < 1000; j++ {
				atomic.AddInt64(&counter, 1)
			}
		}()
	}

	wg.Wait()
	fmt.Println("Final counter value:", counter)
}
```

2. 布尔状态标识
	原子操作也可以用于更新布尔类型的标识状态，如标记一个服务是否已经启动或停止，或者一个操作是否已经完成。可以使用 `atomic.LoadInt32` 和 `atomic.StoreInt32` 来读取和写入布尔状态。
```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

func main() {
	var initialized int32
	var wg sync.WaitGroup
	numGoroutines := 5

	wg.Add(numGoroutines)
	for i := 0; i < numGoroutines; i++ {
		go func(id int) {
			defer wg.Done()
			if atomic.CompareAndSwapInt32(&initialized, 0, 1) {
				fmt.Printf("Goroutine %d initialized the system\n", id)
			} else {
				fmt.Printf("Goroutine %d detected already initialized\n", id)
			}
		}(i)
	}

	wg.Wait()
}
```

3. 无锁读写
	在一些场景下，只有一个线程或协程写数据，而其他多个线程或协程只读取数据。这时可以通过原子操作来安全地读取和更新共享变量。
```go
var sharedVar int64

func read() int64 {
    return atomic.LoadInt64(&sharedVar)
}

func write(val int64) {
    atomic.StoreInt64(&sharedVar, val)
}
```


# ♾️安全指南
## 💫基础
1. 数组使用前，检查长度，防止越界panic
2. 整数溢出问题
```go
// good  
func overflow(numControlByUser int32) {  
    var numInt int32 = 0  
    numInt = numControlByUser + 1  
    if numInt < 0 {  
       fmt.Println(numInt)  
       fmt.Println("integer overflow")  
       return  
    }  
    fmt.Println("integer ok")  
}  
  
func main() {  
    overflow(2147483647)  
}
```

3. slice是引用类型，作为函数入参时，会修改原变量


## 💫文件

1. 检查文件路径的格式，防止“../”访问其他目录
```go
// good: 检查压缩的文件名是否包含..路径穿越特征字符，防止任意写入
func unzipGood(f string) bool {
	r, err := zip.OpenReader(f)
	if err != nil {
		fmt.Println("read zip file fail")
		return false
	}
	for _, f := range r.File {
		if !strings.Contains(f.Name, "..") {
			p, _ := filepath.Abs(f.Name)
			ioutil.WriteFile(p, []byte("present"), 0640)
		} else {
			return false
		}
	}
	return true
}
```

2. 设置文件访问权限

## 💫系统接口
1. 命令执行检查
	- 使用`exec.Command`、`exec.CommandContext`、`syscall.StartProcess`、`os.StartProcess`等函数时，第一个参数（path）直接取外部输入值时，应使用白名单限定可执行的命令范围，不允许传入`bash`、`cmd`、`sh`等命令；
	- 使用`exec.Command`、`exec.CommandContext`等函数时，通过`bash`、`cmd`、`sh`等创建shell，-c后的参数（arg）拼接外部输入，应过滤\n $ & ; | ' " ( ) `等潜在恶意字符；
```go
// good
func checkIllegal(cmdName string) bool {
	if strings.Contains(cmdName, "&") || strings.Contains(cmdName, "|") || strings.Contains(cmdName, ";") ||
		strings.Contains(cmdName, "$") || strings.Contains(cmdName, "'") || strings.Contains(cmdName, "`") ||
		strings.Contains(cmdName, "(") || strings.Contains(cmdName, ")") || strings.Contains(cmdName, "\"") {
		return true
	}
	return false
}
```








