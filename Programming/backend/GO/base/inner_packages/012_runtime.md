#go #package

#### 🔹 Кол-во ядер в ОС

```go
var numCPU = runtime.NumCPU();
```
---
#### 🔹 Получение информации об окружении

```go
func main() {
	fmt.Print("You are using ", runtime.Compiler, " ")
	fmt.Println("on a", runtime.GOARCH, "machine")
	fmt.Println("Using Go version", runtime.Version())
	fmt.Println("Number of CPUs:", runtime.NumCPU())
	fmt.Println("Number of Goroutines:", runtime.NumGoroutine())
}
```
---

#### 🔹 Говорит планировщику переключиться на следущую горутину

```go
runtime.Gosched()
```
---