#go #recipes #package #rabbitmq #queue

Рабочая очередь в Go с RabbitMQ - https://nuancesprog.ru/p/4907/

```bash
# инициализируем модуль
go mod init github.com/romaxa83/rabbit-test

# ставим библиотеку
go get github.com/streadway/amqp

# запускаем консьюмера
go run consumer.go

# при каждом запуске будет отправляться сообщение
go run main.go
```
---
