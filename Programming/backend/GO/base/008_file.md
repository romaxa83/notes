#go 

#### 🔹 Чтение данных их файла

```go
file, err := os.Open("data.txt")
if err != nil {
        log.Fatal(err)
}
scanner := bufio.NewScanner(file)
for scanner.Scan() {
    fmt.Println(scanner.Text())
}
err = file.Close()
if err != nil {
    log.Fatal(err)
}
if scanner.Err() != nil {
    log.Fatal(scanner.Err())
}
```
---
#### 🔹 Вынос в функции открытие/закрытие файла

```go
func OpenFile(fileName string) (*os.File, error) {
    fmt.Println("Opening", fileName)
    return os.Open(fileName)
}

func CloseFile(file *os.File) {
    fmt.Println("Closing file")
    file.Close()
}
```
---
#### 🔹 Получение списка файлов в каталоге

```go
import (
    "fmt"
    "io/ioutil"
    "log"
    "path/filepath"
)

func reportPanic() {
    // Вызываем «recover» и сохр.возвращаемое значение
    p := recover()
    // Если «recover» возвращает nil, паники нет
    if p == nil {
        return
    }

    //В противном случае получаем базовое значение «error», и выводим его
    err, ok := p.(error)
    if ok {
        fmt.Println(err)
    } else {
        // Если значение паники не является признаком ошибки, возобновить панику с тем же значением
        panic(p)
    }
}

func scanDirectory(path string) {
    fmt.Println(path)
    files, err := ioutil.ReadDir(path)
    if err != nil {
        panic(err)
    }

    for _, file := range files {
        // Соединяет путь каталога и имя файла через символ /.
        filePath := filepath.Join(path, file.Name())
        if file.IsDir() {
            // Если это подкаталог рекурсивно вызываем эту же функцию
            scanDirectory(filePath)
        } else {
            fmt.Println("FILE -", filePath)
        }
    }
}

func main() {
    path := "/etc"
    // Прежде чем вызывать код, в котором может возникнуть паника,
    // откладываем вызов новой функции
    defer reportPanic()
    scanDirectory(path)
}
```
---
#### 🔹 Создание и запись в файл

```go
path := "/home/romaxa/projects/go/first_scripts/test.txt"
file, err := os.Create(path)
	if err != nil {
        // здесь перехватывается ошибка
        return
    }
// закрываем файл	
defer file.Close()

//пишем в файл
file.WriteString("ROma")
```
---
#### 🔹 Удаление файлов и каталогов 

> В стандартном пакете os есть методы `Remove()` и `RemoveAll()`, чтобы удалить файлы из каталога.

==пример удаления файла==
```go
import (
    "fmt"
    "os"
)

func main() {
    
    directory := "C:/Go/bin/file/files/"
    
    readDirectory, _ := os.Open(directory)
    allFiles, _ := readDirectory.Readdir(0)
    
    for f := range(allFiles) {
        file := allFiles[f]
        fileName := file.Name()
        filePath := directory + fileName
        
        os.Remove(filePath)
        fmt.Println("Deleted file:", filePath)
    }
}
```
---