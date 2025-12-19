#go #recipes #file

#### 🔹 Чтение текстового файла

```go
package main

import (
	"bufio"
	"flag"
	"fmt"
	"io"
	"os"
)

// чтение по строчно
func lineByLine(file string) error {
	var err error
	f, err := os.Open(file)
	if err != nil {
		return err
	}
	defer f.Close()

	r := bufio.NewReader(f)
	for {
		line, err := r.ReadString('\n')
		if err == io.EOF {
			break
		} else if err != nil {
			fmt.Printf("error reading file %s", err)
			break
		}
		fmt.Print(line)
	}
	return nil
}

// чтение по словам
func wordByWord(file string) error {
	var err error
	f, err := os.Open(file)
	if err != nil {
		return err
	}
	defer f.Close()

	r := bufio.NewReader(f)
	for {
		line, err := r.ReadString('\n')
		if err == io.EOF {
			break
		} else if err != nil {
			fmt.Printf("error reading file %s", err)
			return err
		}
		r := regexp.MustCompile("[^\\s]+")
		words := r.FindAllString(line, -1)
		for i := 0; i < len(words); i++ {
			fmt.Println(words[i])
		}
	}
	return nil
}

// чтение по символам
func charByChar(file string) error {
	var err error
	f, err := os.Open(file)
	if err != nil {
		return err
	}
	defer f.Close()

	r := bufio.NewReader(f)
	for {
		line, err := r.ReadString('\n')
		if err == io.EOF {
			break
		} else if err != nil {
			fmt.Printf("error reading file %s", err)
			return err
		}
		for _, x := range line {
			fmt.Println(string(x))
		}
	}
	return nil
}

func main() {
	flag.Parse()
	if len(flag.Args()) == 0 {
		fmt.Printf("usage: byLine <file1> [<file2> ...]\n")
		return
	}

	for _, file := range flag.Args() {

		// err := lineByLine(file)
		// err := wordByWord(file)
		err := charByChar(file)

		if err != nil {
			fmt.Println(err)
		}
	}
}
```

```bash
# запуск команды
go run main.go test.txt
```
---
#### 🔹 Чтение из `/dev/random`

Назначение устройства `/dev/random` - генерировать случайные данные, которые можно использовать для тестирования программ или, как в данной ситуации, в качестве начального числа для генератора случайных чисел.

Для чтения данных из устройства `/dev/random` нужно  импортировать стандартный Go-пакет `encoding/binary`, поскольку `/dev/random` предоставляет двоичные данные, которые необходимо декодировать.

```go
package main

import (
	"encoding/binary"
	"fmt"
	"os"
)

func main() {

	f, err := os.Open("/dev/random")
	defer f.Close()
	if err != nil {
		fmt.Println(err)
		return
	}
	var seed int64
	binary.Read(f, binary.LittleEndian, &seed)
	fmt.Println("Seed:", seed)
}
```
---

