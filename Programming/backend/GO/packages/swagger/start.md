#go #api-docs #recipes #package

#### 🔹 Cтавим `swagger` глобально на комп

я ставил так (разные варианты https://goswagger.io/install.html)
```bash
dir=$(mktemp -d) 
git clone https://github.com/go-swagger/go-swagger "$dir" 
cd "$dir"
go install ./cmd/swagger

# проверяем
swagger version
```

==создаем проект==
```bash
mkdir tg-bot && cd tg-bot
go mod init github.com/romaxa83/tg-bot 
mkdir api && cd api && touch api.yml
```

==в файле api.yml описываем  `swagger`==
```bash
# команда генерирует нужные файлы для дальнейшей работы на основе api.yml
swagger generate server -f api/api.yml -A "tg-bot" --server-package=internal/handler
```

Единственный файл который можно редактировать, из тех файлов которые сгенерировались, это - `internal/handler/configure_tg_bot.go`, т.к. при повторной генерации остальные файлы будут перегенерироваться кроме данного

==запуск сервера==

```bash
# из корня проекта (при первом запуске прийдеться ставить зависимости)
go run cmd/tg-bot-server/main.go 

# api доступно по 
http://127.0.0.1:37459
```

==логику по роута прописываеться в файле /internal/handler/configure_tg_bot.go==
```go
// приблизительно такой хендлер
if api.PostSendHandler == nil {
	api.PostSendHandler = operations.PostSendHandlerFunc(func(params operations.PostSendParams) middleware.Responder {
		return middleware.NotImplemented("operation operations.PostSend has not yet been implemented")
	})
}
```
---