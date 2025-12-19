#go #recipes #config

#### 🔹 Формат для конфигураций

==test.yaml==
```yaml
hits: 55
time: 125615627
```

==пример парсинга yml файла==
```go
import (
	"fmt"
	"gopkg.in/yaml.v3"
	"io/ioutil"
)

type Conf struct {
	Hits int64 `yaml:"hits"`
	Time int64 `yaml:"time"`
}

func main() {
	config := &Conf{}

	yamlFile, err := ioutil.ReadFile("test.yaml")
	if err != nil {
		panic(err)
	}
	err = yaml.Unmarshal(yamlFile, config)
	if err != nil {
		panic(err)
	}

	fmt.Printf("%+v\n", config)
}

//  создание 
func main() {
	config := &Conf{
		Hits:11,
		Time:115865674576,
	}

	out, _ := yaml.Marshal(config)

	fmt.Println(string(out))
}
```
---
#### 🔹 Маршалинг якорей в `yaml`

==пример yaml файла, т.е. описаный &build-test должен вставиться на место *build-test==
```yaml
definitions:
  steps:
    - step: &build-test
      name: Build and test
      script:
        - mvn package
      artifacts:
        - target/**

pipelines:
  branches:
    develop:
      - step: *build-test
    main:
      - step: *build-test

```

 ==unmarshal развернет в нужную структуру==
```go
type Conf struct {
	Definitions map[string]interface{} `yaml:"definitions"`
	Pipelines   map[string]interface{} `yaml:"pipelines"`
}

func main() {
	config := &Conf{}

	yamlFile, err := ioutil.ReadFile("test.yaml")
	if err != nil {
		panic(err)
	}
	err = yaml.Unmarshal(yamlFile, config)
	if err != nil {
		panic(err)
	}

	fmt.Printf("%+v\n", config)
}
```
---
#### 🔹 Кастомный `unmarshal` для поле

Есть такая конструкция , нам нужно теги распарсить в слайс.

```yaml
messages:
  tags: Main, Important, Test
```

```go
type SliceTags []string

func (tags *SliceTags) UnmarshalYAML(value *yaml.Node) error {
	if value != nil {
		*tags = strings.Split(value.Value, ",")
	}
	return nil
}

type Messages struct {
	Tags SliceTags `yaml:"tags"`
}

type Subs struct {
	Messages Messages `yaml:"messages"`
}

func main() {
	config := &Subs{}

	yamlFile, err := ioutil.ReadFile("test.yaml")
	if err != nil {
		panic(err)
	}
	err = yaml.Unmarshal(yamlFile, config)
	if err != nil {
		panic(err)
	}

	fmt.Println(config)
}
```
---
