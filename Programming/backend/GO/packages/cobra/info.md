#go #package #terminal 

#### 🔹 Создание терминального приложения

Используется пакет `cobra`. Для синтаксического анализа аргументов командной строки в `cobra` по умолчанию используется пакет `viper`.

 - https://dev.to/aurelievache/learning-go-by-examples-part-3-create-a-cli-app-in-go-1h43

```bash
cd ~/go/src/github.com/romaxa83
mkdir my-cli
cd my-cli

# инициализируем модуль (создаст go.mod)
go mod init github.com/romaxa83/my-cli

mkdir bin
touch README.md

# ставим пакет cobra (для создания консольных приложений)
# @see https://github.com/spf13/cobra
go get -u github.com/spf13/cobra/cobra

# инициализация приложение через пакета cobra
cobra init --pkg-name github.com/romaxa83/my-cli

# устанавливаем зависимый пакет
go get github.com/spf13/viper@v1.8.1

# добавление команда в приложении (в примере command-name) создаст одноименой файл в папке cmd
cobra add command-name

# вызвать команду
go run main.go command-name

# собираем через Taskfile (ниже)
task build

# смотрим исполняемый файл
ll bin/gopher-cli
```

#### 🔹 Build app

- https://taskfile.dev/#/installation

==Создаем Taskfile.yml файл примерно с такими шагами для сборки==
```yaml
version: "3"

tasks:
    build:
        desc: Build the app
        cmds:
        - GOFLAGS=-mod=mod go build -o bin/my-cli main.go 

    run: 
        desc: Run the app
        cmds:
        - GOFLAGS=-mod=mod go run main.go

    clean:
        desc: Remove all retrieved *.png files
        cmds:
        - rm *.png

```
---
#### 🔹 Передача аргументов

Создаем команду (к примеру `cmdOne`) в файле `cmd/cmdOne.go`

```go
var cmdOneCmd = &cobra.Command{
    Use: "cmdOne",
    Short:   "A brief description of your command",
    Long: `A longer `,
    Run: func(cmd *cobra.Command, args []string) {
        fmt.Println("cmdOne called")

        // здесь получаем аргумент
        number, _ := cmd.Flags().GetInt("number")
        fmt.Println("Going to use number", number)
        fmt.Printf("Square: %d\n", number*number)
    },
}

func init() {
    rootCmd.AddCommand(cmdOneCmd)

    // определяем новый флаг
    cmdOneCmd.Flags().Int("number", 0, "A help for number")
}
```

```bash
# теперь вызываем команду так
go run main.go cmdOne --number=6
```
---
#### 🔹 Создаем алиасы для команд

Создаем команду (к примеру `cmdOne`) в файле `cmd/cmdOne.go`
```go
var cmdOneCmd = &cobra.Command{
    Use: "cmdOne",

    // добавляем алиас для комманды
    Aliases: []string{"one", "cone"},
    Short:   "A brief description of your command",
    Long: `A longer `,
    Run: func(cmd *cobra.Command, args []string) {
        fmt.Println("cmdOne called")
    },
}

func init() {
    rootCmd.AddCommand(cmdOneCmd)
}
```

```bash
# теперь вызываем команду такими способами
go run main.go cmdOne
go run main.go one
go run main.go cone
```
---
