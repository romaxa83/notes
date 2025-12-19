#go #recipes #rabbitmq 

```go
package main

import (
	"fmt"
	"github.com/streadway/amqp"
)

func main() {
	fmt.Println("Consumer Application")

	conn, err := amqp.Dial("amqp://rabbit:rabbit@192.168.143.1:5672/")
	if err != nil {
		fmt.Println(err)
		panic(err)
	}
	defer conn.Close()

	ch, err := conn.Channel()
	if err != nil {
		fmt.Println(err)
		panic(err)
	}
	defer ch.Close()

	msgs, err := ch.Consume(
		"TestQueue",
		"",
		true,
		false,
		false,
		false,
		nil,
	)
	if err != nil {
		fmt.Println(err)
		panic(err)
	}

	forever := make(chan bool)
	go func() {
		for d := range msgs {
			fmt.Printf("Recieved message: %s\n", d.Body)
		}
	}()

	fmt.Println("Successfully connected to our RabbitMQ")
	fmt.Println(" [*] - waiting for message")
	<-forever
}
```