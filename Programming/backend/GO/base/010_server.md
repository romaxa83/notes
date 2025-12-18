#go #server

#### 🔹 Simple server

```go
import (
    "log"
    "net/http"
)

func viewHandler(writer http.ResponseWriter, request *http.Request) {
    message := []byte("Hello, web!")
    _, err := writer.Write(message)
    if err != nil {
        log.Fatal(err)
    }
}

// где
// writer - Значение для обновления ответа, которое будет отправлено браузеру.
// request - Значение, представляющее запрос от браузера.

func main() {
    http.HandleFunc("/hello", viewHandler)
    err := http.ListenAndServe("localhost:8080", nil)
    log.Fatal(err)
}
```
---
#### 🔹 Другой вариант

```go
import ("net/http" ; "io")

func hello(res http.ResponseWriter, req *http.Request) {
    res.Header().Set(
        "Content-Type", 
        "text/html",
    )
    io.WriteString(
        res, 
        `<doctype html>
            <html>
            <head>
                <title>Hello World</title>
            </head>
            <body>
                Hello World!
            </body>
            </html>`,
    )
}

func main() {
    http.HandleFunc("/hello", hello)
    http.ListenAndServe(":9000", nil)
}
```
---