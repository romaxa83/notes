#go #package

#### 🔹 `RuneCountInString()`

Подсчитывает кол-во рун (символов в строке)
```go
import "unicode/utf8"

title:= "some text"
fmt.Println(utf8.RuneCountInString(title))
```
---
#### 🔹 `IsPrint()`

Функция `unicode.IsPrint()`, которая возвращает `true`, если руну можно напечатать, и `false`  - если нельзя.

```go
const sLiteral = "\x99\x42\x32\x55\x50\x35\x23\x50\x29\x9c"

for i := 0; i < len(sLiteral); i++ {
	if unicode.IsPrint(rune(sLiteral[i])) {
		fmt.Printf("%c\n", sLiteral[i])
	} else {
		fmt.Println("Not printable!")
	}
}
```
---
