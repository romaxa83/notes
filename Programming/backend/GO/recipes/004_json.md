#go #recipes

#### 🔹 Пример парсинга `json` в структуру

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Book struct {
	Title  string `json:"title"`
	Author Author `json:"author"`
}

type Author struct {
	Name      string `json:"name"`
	Age 	  int    `json:"age"`
	Developer bool   `json:"is_developer"`
}

func main()  {
	fmt.Println("json")
	author := Author{
		Name: "Jack London",
		Age: 55,
		Developer: true,
	}
	book := Book {
		Title: "Martin Eden",
		Author: author,
	}

	//byteArr, _ := json.Marshal(book)
	toJson, _ := json.MarshalIndent(book, "", " ")
	fmt.Println(string(toJson))

	jsonString := `{"title": "Some book", "author": {"name": "test", "age": 32, "is_developer": "true"}}`
	var reading Book
	json.Unmarshal([]byte(jsonString), &reading)
	fmt.Printf("%#v \n", reading)
	fmt.Println("================================")
}
```
---
#### 🔹 Чтение из файла

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type Record struct {
	Name string
	Surname string
	Tel []Telephone
}

type Telephone struct {
	Mobile bool
	Number string
}

func loadFromJSON(filename string, key interface{}) error {
	in, err := os.Open(filename)
	if err != nil {
		return err
	}

	decodeJSON := json.NewDecoder(in)
	err = decodeJSON.Decode(key)
	if err != nil {
		return err
	}
	in.Close()
	return nil
}

func main() {
	arguments := os.Args
	if len(arguments) == 1 {
		fmt.Println("Please provide a filename!")
		return
	}
	filename := arguments[1]

	var myRecord Record
	err := loadFromJSON(filename, &myRecord)
	if err == nil {
		fmt.Println(myRecord)
	} else {
		fmt.Println(err)
	}
}
```


==создаем файл read.json, с таким содержанием==
```json
{
	"Name":"Mihalis",
	"Surname":"Tsoukalos",
	"Tel":[
		{"Mobile":true,"Number":"1234-567"},
		{"Mobile":true,"Number":"1234-abcd"},
		{"Mobile":false,"Number":"abcc-567"}
	]
}
```

```bash
# запускаем команду 
go run main.go read.json
```
---
#### 🔹 Запись в `json`

В примере записывает данные в стандартный поток вывода ( `os.Stdout` ), то есть выводит данные в окно терминала.

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type Record struct {
	Name string
	Surname string
	Tel []Telephone
}

type Telephone struct {
	Mobile bool
	Number string
}

func saveToJSON(filename *os.File, key interface{}) {
	encodeJSON := json.NewEncoder(filename)
	err := encodeJSON.Encode(key)
	if err != nil {
		fmt.Println(err)
		return
	}
}

func main() {

	myRecord := Record{
		Name:
		"Mihalis",
		Surname:
		"Tsoukalos",
		Tel: []Telephone{Telephone{Mobile: true, Number: "1234-567"},
			Telephone{Mobile: true, Number: "1234-abcd"},
			Telephone{Mobile: false, Number: "abcc-567"},
		},
	}
	saveToJSON(os.Stdout, myRecord)
}
```
---
#### 🔹 Использование `Marshal()` и `Unmarshal()`

> Могут реализовывать тот же функционал, что `Decode` и `Encode`

==пример схожий на верхние==
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type Record struct {
	Name string
	Surname string
	Tel []Telephone
}

type Telephone struct {
	Mobile bool
	Number string
}

func main() {

	myRecord := Record{
		Name: "Mihalis",
		Surname: "Tsoukalos",
		Tel: []Telephone{
			Telephone{Mobile: true, Number: "1234-567"},
			Telephone{Mobile: true, Number: "1234-abcd"},
			Telephone{Mobile: false, Number: "abcc-567"},
		},
	}
	rec, err := json.Marshal(&myRecord)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(rec))

	var unRec Record
	err1 := json.Unmarshal(rec, &unRec)
	if err1 != nil {
		fmt.Println(err1)
		return
	}
	fmt.Println(unRec)
}
```

`json.Marshal()` и `json.Unmarshal()` требует указатель в качестве аргумента.

Функция `json.Marshal()` возвращает срез байт `[]byte`, который после записи обычно становится не нужен и впоследствии удаляется сборщиком мусора. Если ваша программа ориентирована на массовую обработку и запись JSON, то постоянное выделение и освобождение `[]byte` создает огромную нагрузку на сборщик мусора. Для снижения этой нагрузки лучше применять `json.NewEncoder().Encode()`, который задействует стандартный пакет `sync.Pool` с целью повторного использования срезов байт `[]byte` для маршализации JSON.

---
#### 🔹 Анализ данных в формате JSON

В примерах выше, при чтении `json`, заранее была известна его структура, поэтому применялись `struct`, если заранее не известна структура, нужно использовать 
хеш-таблицы (`map`), пример ниже.

```go
import (
	"encoding/json"
	"fmt"
	"io/ioutil"
	"os"
)

func main() {
	arguments := os.Args
	if len(arguments) == 1 {
		fmt.Println("Please provide a filename!")
		return
	}
	filename := arguments[1]

	// читаем весь файл сразу
	fileData, err := ioutil.ReadFile(filename)
	if err != nil {
		fmt.Println(err)
		return
	}
	// Значение каждого ключа имеет тип interface{}, 
	// который может быть любым
	var parsedData map[string]interface{}
	json.Unmarshal([]byte(fileData), &parsedData)

	for key, value := range parsedData {
		fmt.Println("key:", key, "value:", value)
	}
}
```


==Если у нас большой json с множеством полей, а нам нужно всего лишь смапить одно поле==
```go
func LoadAndParseRawMsg() {
	jsonData, _ := os.ReadFille("examples/example.json")
	var objmap map[string]json.RawMessage
	json.Unmarshal(jsonData, &objmap)
	fmt.Println(objmap)
	var internalMap map[string]string
	json.Unmarshal(objmap["request"], &internalMap)
	fmt.Println(internalMap)
}
```

==исходный json==
```go
{
	"request": {
		"user": "Admin",
		"msg": "send message"
	},
	"user": "admin"
}
```
---
#### 🔹 Кастомно мапить 

В чем суть, у нас есть `json` есть такое поле , как `tags` в примере, где значения идут через запятую а нам нужно замапить, в процессе маршалинг, в массив (точнее слайс).

```json
{
	"request": {
		"user": "Admin",
		"msg": "send message",
		"tags": "main, important, test"
	},
	"user": "admin"
}
```

==пример (мы создадим тип поля (strslice), и реализуем метод который будет сплитовать строку)==
```go
type strslice []string

func (ss *strslice) UnmarshalJSON(data []byte) error {
	var s string
	if err := json.Unmarshal(data, &s); err != nil {
		return err
	}
	*ss = strings.Split(s, ",")
	return nil
}

type RequestContentTag struct {
	User    string
	Message string   `json:"msg"`
	Tags    strslice `json:"tags"`
}

type RequestTaggged struct {
	Request RequestContentTag
	Author  string `json:"user"`
}

func main() {
	jsonData, err := os.ReadFile("test.json")
	fmt.Println(err)
	var request RequestTaggged
	fmt.Println(json.Unmarshal(jsonData, &request))
	fmt.Printf("%+v\n", request)
}
```
---

