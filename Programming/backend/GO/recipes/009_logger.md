#go #recipes #log

Команда пишет логи в файл

```go
package main

import (
	"fmt"
	"log"
	"os"
)

// здесь может быть любой путь
var LOGFILE = "/tmp/mGo.log"

func main() {
	f, err := os.OpenFile(LOGFILE, os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0644)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer f.Close()

	iLog := log.New(f, "customLogLineNumber ", log.LstdFlags)
	iLog.SetFlags(log.LstdFlags | log.Lshortfile)
	iLog.Println("Hello there!")
	iLog.Println("Another log entry!")
}
```

```bash
go run main.go
cat /tmp/mGo.log
```
---