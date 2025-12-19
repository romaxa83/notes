#go #framework #recipes #package

#### 🔹 Инициализация проекта

```bash
mkdir gin-app && cd gin-app
go mod init github.com/romaxa83/gin-app
go mod tiny
git init
touch .gitignore
echo ".idea" > .gitignore

# в папке cmd храниться файл main.go 
mkdir cmd && touch cmd/main.go

# в папке pkg храниться вся логика приложения 
mkdir -p pkg/handler && touch pkg/handler/handler.go 
mkdir -p pkg/service
mkdir -p pkg/repository

# содержимое в img/start/server
touch server.go

go get -u github.com/gin-gonic/gin

# создаем структуры
touch user.go todo.go

touch pkg/handler/handler.go
touch pkg/handler/auth.go
touch pkg/handler/list.go
touch pkg/handler/item.go

# запуск приложения
go run cmd/main.go


# стркуктура проекта
Http запрос
	|
Handler
	|
Service (бизнес логика)
	|
Repository (работа с бд)

# комуникация между слоями будет происходить через интерфейсы
```
---
#### 🔹 Config

```bash
mkdir configs && touch configs/config.yml
touch .env.dist .env

go get -u github.com/spf13/viper
go get -u github.com/joho/godotenv
```
---
#### 🔹Коллекция мидлеваров

- https://openbase.com/go/github.com/gin-gonic/contrib

---
#### 🔹 Docker

```bash
mkdir storage && touch storage/.gitignore
echo "*" > storage/.gitignore
echo "!.gitignore" > storage/.gitignore

touch docker-compose.yml
touch Makefile
```
---
#### 🔹DB

```bash
# устанавливаем утилиту migrate

migrate create -ext sql -dir ./schema -seq init

# запуск миграции
migrate -path ./schema -database 'postgres://root:root@192.168.142.1:5432/db?sslmode=disable' up

# изменеие "грязных" миграций
select * from schema_migrations;
update schema_migrations set version='000001', dirty=false;

# библиотека для работы с бд
go get -u github.com/jmoiron/sqlx
```
---
#### 🔹 Logger

```bash
go get -u github.com/sirupsen/logrus
```
---
#### 🔹 JWT

```bash
go get -u github.com/dgrijalva/jwt-go
```
---
#### 🔹 Swagger

```bash
go get -u github.com/swaggo/swag/cmd/swag
```


В `main.go` описываем, через аннотацию данных по проекту

```bash
# генерируем доку
swag init -g cmd/main.go
```

```go
// там где определяем роуты, импортируем данные библиотеки
import "github.com/swaggo/gin-swagger" // gin-swagger middleware
import "github.com/swaggo/files" // swagger embed files

// прописуем в импорте путь к папке docs, сгенерированой свагером
_ "github.com/romaxa83/gin-app/docs"

// и прописываем роут, по которому будет доступна документация
r.GET("/swagger/*any", ginSwagger.WrapHandler(swaggerFiles.Handler))
```
---
#### 🔹 Prometheus & Grafana

- дашборд для go - grafana.com/grafana/dashboards/6671
- https://prometheus.io/docs/guides/go-application/
---
#### 🔹 Test

Для тестирования хендлеров, нужно реализовать моки для сервисов, чтобы изолировать логику хендлеров моки можно писать самому или использовать стороние решения.

```bash
# пакет для генерации моков
go install github.com/golang/mock/mockgen@v1.6.0
go get -u github.com/golang/mock/gomock

# в файле, где описаны интерфейсы прописываем данную строчку, которая генерирует моки по интерфейсам
go:generate mockgen -source=services.go -destination=mocks/mock.go

# переходим в файл, где находиться данный коментраий и запускаем генерацию
go generate
```
---
#### 🔹 i18n

```bash
// устанавливаем cli приложение
go get -u github.com/nicksnyder/go-i18n/v2/goi18n

// генерируем базовый файл
goi18n extract -outdir translate_folder

// создаем файл для другого перевода
touch translate.ru.toml
```
---
#### 🔹 Jager

```bash
go get -u github.com/opentracing/opentracing-go
go get -u github.com/uber/jaeger-client-go/config
```
---

