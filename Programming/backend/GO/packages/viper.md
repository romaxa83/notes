#go #package #recipes #config

#### 🔹 Пакет устанавливается

```bash
go get -u github.com/spf13/viper
```
---
#### 🔹  Простой пример

==читать и изменять переменные среды, используя viper==
```go
package main

import (
	"fmt"
	"github.com/spf13/viper"
)

func main() {
	viper.BindEnv("GO_TEST_VAR")
	val := viper.Get("GO_TEST_VAR")
	fmt.Println("GO_TEST_VAR:", val)

	viper.Set("GO_TEST_VAR", 10)
	val = viper.Get("GO_TEST_VAR")
	fmt.Println("GO_TEST_VAR:", val)

	viper.BindEnv("NEW_VARIABLE")
	val = viper.Get("NEW_VARIABLE")
	if val == nil {
		fmt.Println("NEW_VARIABLE not defined.")
		return
	}
	fmt.Println(val)
}
```
---
#### 🔹 Пример для работы с аргументами командной строки

```bash
# нужноимпортировать пакет pflag
go get -u github.com/spf13/pflag

# запуск
go run main.go
go run main.go -i 45
```

```go
package main

import (
	"flag"
	"fmt"
	"github.com/spf13/pflag"
	"github.com/spf13/viper"
)

func main() {
	// если запустить без параметров, по дефолту будет использован данный
	flag.Int("i", 100, "i parameter")
	pflag.CommandLine.AddGoFlagSet(flag.CommandLine)
	pflag.Parse()

	viper.BindPFlags(pflag.CommandLine)
	i := viper.GetInt("i")
	fmt.Println(i)
}
```
---
#### 🔹 Чтение конфигурационых файлов в формате `JSON`

==создаем в этой же директории файл json с конфигурацией с таким содержанием==
```json
{
    "item1": {
        "key1": "val1",
        "key2": false,
        "key3": 155
    },
    "item2": {
        "key1": "val1",
        "key2": true,
        "key3": 12
    }
}
```

```go
package main

import (
	"fmt"
	"github.com/spf13/viper"
)

func main() {
	viper.SetConfigType("json")
	viper.SetConfigFile("./myJSONConfig.json")
	fmt.Printf("Using config: %s\n", viper.ConfigFileUsed())
	viper.ReadInConfig()

	if viper.IsSet("item1.key1") {
		fmt.Println("item1.key1:", viper.Get("item1.key1"))
	} else {
		fmt.Println("item1.key1 not set!")
	}
	if viper.IsSet("item2.key3") {
		fmt.Println("item2.key3:", viper.Get("item2.key3"))
	} else {
		fmt.Println("item2.key3 is not set!")
	}

	if !viper.IsSet("item3.key1") {
		fmt.Println("item3.key1 is not set!")
	}
}
```

Если программа не находит `myJSONConfig.json` , то она не сообщит об ошибке, а будет действовать так, как будто прочитала пустой конфигурационный файл

---
#### 🔹 Чтение конфигурационых файлов в формате YAML

На этот раз имя конфигурационого YAML-файла будет  передаваться скрипту в виде аргумента командной строки. Кроме того, в скрипте будет использоваться функция `viper.AddConfigPath()` для добавления трех путей поиска, по которым viper будет  автоматически искать конфигурационые файлы.

==создаем в этой же директории файл myYamlConfig.yaml с таким содержанием==
```yaml
item1:
  k1:
    - true
  k2:
    - myValue
```

```go
package main

import (
	"fmt"
	flag "github.com/spf13/pflag"
	"github.com/spf13/viper"
	"os"
)

func main() {
	var configFile *string = flag.String("c", "myConfig",
		"Setting the configuration file")
	flag.Parse()

	_, err := os.Stat(*configFile)
	if err == nil {
		fmt.Println("Using User Specified Configuration file!")
		viper.SetConfigFile(*configFile)
	} else {
		viper.SetConfigName(*configFile)
		viper.AddConfigPath("/tmp")
		viper.AddConfigPath("$HOME")
		viper.AddConfigPath(".")
	}

	err = viper.ReadInConfig()
	if err != nil {
		fmt.Printf("%v\n", err)
		return
	}
	fmt.Printf("Using config: %s\n", viper.ConfigFileUsed())

	if viper.IsSet("item1.k1") {
		fmt.Println("item1.val1:", viper.Get("item1.k1"))
	} else {
		fmt.Println("item1.k1 not set!")
	}
	if viper.IsSet("item1.k2") {
		fmt.Println("item1.val2:", viper.Get("item1.k2"))
	} else {
		fmt.Println("item1.k2 not set!")
	}
	if !viper.IsSet("item3.k1") {
		fmt.Println("item3.k1 is not set!")
	}
}
```

```bash
# запускаем
go run main.go --c myYamlConfig.yaml
go run main.go

```
---
