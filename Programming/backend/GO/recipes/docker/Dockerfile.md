```Dockerfile
FROM golang:alpine

# создаем новый каталог
RUN mkdir /files

#копируют в образ Docker файл ( hw.go ) из текущего каталога
COPY main.go /files

#изменяют текущий рабочий каталог образа Docker.
WORKDIR /files

# создаем бинарник
RUN go build -o /files/main main.go

#задаем путь к бинарнику, который будет выполняться при запуске образа Docker.
ENTRYPOINT ["/files/main"]
```