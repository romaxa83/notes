#go #recipes #file

> В скрипте представленны 5 способов записи в файл.

```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"io/ioutil"
	"os"
)

func main() {
	s := []byte("Data to write\n")

	// в этом блоке записываем в файл с помощью fmt.Fprintf()
	f1, err := os.Create("f1.txt")
	if err != nil {
		fmt.Println("Cannot create file", err)
		return
	}
	defer f1.Close()

	fmt.Fprintf(f1, string(s))

	// в этом блоке записываем в файл с помощью WriteString()
	f2, err := os.Create("f2.txt")
	if err != nil {
		fmt.Println("Cannot create file", err)
		return
	}
	defer f2.Close()
	n, err := f2.WriteString(string(s))
	fmt.Printf("wrote %d bytes\n", n)

	// В данном случае функция bufio.NewWriter() открывает файл для записи,
	// а bufio.WriteString() записывает данные.
	f3, err := os.Create("f3.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	w := bufio.NewWriter(f3)
	n, err = w.WriteString(string(s))
	fmt.Printf("wrote %d bytes\n", n)
	w.Flush()

	// в данном блоке функции ioutil.WriteFile() для записи
	// данных, и не требует использования os.Create()
	f4 := "f4.txt"
	err = ioutil.WriteFile(f4, s, 0644)
	if err != nil {
		fmt.Println(err)
		return
	}

	// в данном блоке используется функция io.Wri­teString()
	f5, err := os.Create("f5.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	n, err = io.WriteString(f5, string(s))
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Printf("wrote %d bytes\n", n)
}
```
---