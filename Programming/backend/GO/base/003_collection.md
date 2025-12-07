#go 

### 🔹 ARRAY

> это нумерованная последовательность элементов одного типа с фиксированной длиной. Массивы в Go имеют фиксированный размер, они не могут увеличиваться или уменьшаться.

##### 🔸 Базовое объявление
```go
// обьявление массива x с длиной 5 элементов типом int,все не проинициализированые эл. массива будут иметь нулевое значение, т.е. int - это 0, для string - пустая строка, для bool - false, соответсвено без инициализации, с эл. можно производить какие то действия
var x [5]int // [0 0 0 0 0]

x[4] = 100
fmt.Println(x) // [0 0 0 0 100]

x[2]++
x[1]--
fmt.Println(x) // [0 -1 1 0 100]

// Объявление с инициализацией  
var fruits [3]string = [3]string{"apple", "banana", "orange"}  
fmt.Println(fruits) // [apple banana orange]  
  
// Короткая форма объявления  
colors := [4]string{"red", "green", "blue", "yellow"}  
fmt.Println(colors) // [red green blue yellow]

var dates [2]time.Time
dates[0] = time.Unix(1257894000, 0)
dates[1] = time.Unix(1257894000, 0)
fmt.Println(dates)
```

##### 🔸 Автоматическое определение размера
```go
// Компилятор сам определит размер  
numbers := [...]int{1, 2, 3, 4, 5}  
fmt.Printf("Length: %d\n", len(numbers)) // Length: 5  
  
// Инициализация по индексам  
sparse := [10]int{2: 100, 5: 200, 9: 300}  
fmt.Println(sparse) // [0 0 100 0 0 200 0 0 0 300]
```
------
#### 🔹 Основные операции с массивами
##### 🔸 Доступ к элементам
```go
arr := [5]int{10, 20, 30, 40, 50}  
  
// Чтение элементов  
fmt.Println("First element:", arr[0])  // 10  
fmt.Println("Last element:", arr[4])   // 50  
  
// Изменение элементов  
arr[2] = 99  
fmt.Println("Modified array:", arr) // [10 20 99 40 50]  
  
// Получение длины  
fmt.Println("Length:", len(arr)) // 5

// детальная информация о массиве
fmt.Printf("%#v\n", arr)
```

##### 🔸 Итерация по массивам
```go
numbers := [4]int{1, 2, 3, 4}  
  
// Обычный for цикл  
for i := 0; i < len(numbers); i++ {  
    fmt.Printf("Index %d: %d\n", i, numbers[i])  
}  
  
// Range цикл - получаем индекс и значение  
for index, value := range numbers {  
    fmt.Printf("Index %d: %d\n", index, value)  
}  
  
// Range цикл - только значения  
for _, value := range numbers {  
    fmt.Printf("Value: %d\n", value)  
}  
  
// Range цикл - только индексы  
for index := range numbers {  
    fmt.Printf("Index: %d\n", index)  
}
```
---
#### 🔹 Многомерные массивы
##### 🔸 Двумерные массивы
```go
// Объявление двумерного массива  
var matrix [3][4]int  
  
// Инициализация двумерного массива  
table := [2][3]int{  
    {1, 2, 3},  
    {4, 5, 6},  
}  
fmt.Println(table) // [[1 2 3] [4 5 6]]  
  
// Доступ к элементам  
fmt.Println("Element [1][2]:", table[1][2]) // 6  
  
// Изменение элементов  
table[0][1] = 99  
fmt.Println("Modified:", table) // [[1 99 3] [4 5 6]]
```

##### 🔸 Работа с многомерными массивами
```go
func main() {
    // Трёхмерный массив
    cube := [2][2][2]int{
        {
            {1, 2},
            {3, 4},
        },
        {
            {5, 6},
            {7, 8},
        },
    }

    // Итерация по многомерному массиву
    for i, plane := range cube {
        for j, row := range plane {
            for k, value := range row {
                fmt.Printf("cube[%d][%d][%d] = %d\n", i, j, k, value)
            }
        }
    }
}
```
---
#### 🔹 Передача массивов в функции
##### 🔸 Массивы передаются по значению
```go
func modifyArray(arr [5]int) {
    arr[0] = 999
    fmt.Println("Inside function:", arr) // [999 20 30 40 50]
}

func modifyArrayByPointer(arr *[5]int) {
    arr[0] = 999
    fmt.Println("Inside function:", *arr) // [999 20 30 40 50]
}

func main() {
    original := [5]int{10, 20, 30, 40, 50}

    // Передача по значению - оригинал не изменится
    modifyArray(original)
    fmt.Println("After value pass:", original) // [10 20 30 40 50]

    // Передача по указателю - оригинал изменится
    modifyArrayByPointer(&original)
    fmt.Println("After pointer pass:", original) // [999 20 30 40 50]
}
```
##### 🔸 Возврат массивов из функций
```go
func createArray() [3]string {
    return [3]string{"Go", "is", "awesome"}
}

func initializeMatrix() [2][2]int {
    return [2][2]int{
        {1, 2},
        {3, 4},
    }
}

func main() {
    words := createArray()
    fmt.Println(words) // [Go is awesome]

    matrix := initializeMatrix()
    fmt.Println(matrix) // [[1 2] [3 4]]
}
```
---
#### 🔹 Сравнение массивов
```go
func main() {
    arr1 := [3]int{1, 2, 3}
    arr2 := [3]int{1, 2, 3}
    arr3 := [3]int{3, 2, 1}

    // Массивы одинакового типа можно сравнивать
    fmt.Println("arr1 == arr2:", arr1 == arr2) // true
    fmt.Println("arr1 == arr3:", arr1 == arr3) // false

    // Массивы разных размеров нельзя сравнивать
    // var arr4 [4]int
    // fmt.Println(arr1 == arr4) // Ошибка компиляции
}
```
---
#### 🔹 Копирование массивов
```go
func main() {
    original := [4]string{"a", "b", "c", "d"}

    // Присваивание создаёт копию
    copy1 := original
    copy1[0] = "modified"

    fmt.Println("Original:", original) // [a b c d]
    fmt.Println("Copy:", copy1)        // [modified b c d]

    // Использование copy() для слайсов от массивов
    var dest [4]string
    copy(dest[:], original[:])
    fmt.Println("Dest:", dest) // [a b c d]
}
```
---
#### 🔹 Ограничения массивов в Go
```go
func main() {
    // 1. Размер является частью типа
    var arr1 [5]int
    var arr2 [10]int
    // arr1 = arr2 // Ошибка: разные типы

    // 2. Размер должен быть известен на этапе компиляции
    // var size int = 5
    // var arr [size]int // Ошибка: размер должен быть константой

    // 3. Правильный способ
    const size = 5
    var arr [size]int
    fmt.Println(len(arr)) // 5
}
```
---
### 🔹 SLICE
разновидность коллекции которую можно расширять в отличие от массива, но обладает всеми свойствами массива, т.к. построены на основе массивов при создании сегмента, за кулисами создается базовый массив (в котором хранятся эл. сегмента), т.е. слайс - это ссылка на часть массива. ==Структура слайса: указатель, длина, ёмкость.==

Срезы характеризуются двумя основными свойствами: емкостью (`capacity`) и длиной (`length`). Длина среза равна длине массива с таким же количеством элементов и может быть получена с помощью функции `len()`. ==Емкость среза== — это то, сколько места в памяти занимает срез в данный момент. Емкость можно узнать с помощью функции `cap()`, если длина и емкость среза имеют одинаковые значения, ==то попытка добавить в срез еще один элемент приводит к тому, что емкость среза будет удвоена, тогда как его длина увеличится только на единицу==

```go
// Создание пустого слайса  
var numbers []int  
fmt.Println("Empty slice:", numbers)        // []  
fmt.Println("Length:", len(numbers))        // 0  
fmt.Println("Capacity:", cap(numbers))      // 0  
fmt.Println("Is nil:", numbers == nil)      // true

// проверка среза на пустоту
len(numbers) == 0
```

##### 🔸 Создание слайсов
```go
func main() {
    // 1. Литерал слайса
    fruits := []string{"apple", "banana", "orange"}
    fmt.Println("Fruits:", fruits) // [apple banana orange]

    // 2. Make функция
    numbers := make([]int, 3)      // длина = 3, ёмкость = 3
    fmt.Println("Made slice:", numbers) // [0 0 0]

    // 3. Make с указанием ёмкости
    buffer := make([]int, 0, 5)    // длина = 0, ёмкость = 5
    fmt.Printf("Buffer: %v, len: %d, cap: %d\n", buffer, len(buffer), cap(buffer))

    // 4. Из массива (срез)
    arr := [5]int{1, 2, 3, 4, 5}
    slice := arr[1:4]              // элементы с индекса 1 до 3
    fmt.Println("From array:", slice) // [2 3 4]
}
```
---
#### 🔹 Операции среза (slicing)
##### 🔸 Базовый синтаксис среза

> срез не хранит значения (они хранятся в базовом массиве) он лишь ссылается на базовый массив

```go
func main() {  
    data := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}  
  
    // slice[start:end] - от start включительно до end не включительно  
    fmt.Println("data[2:5]:", data[2:5])    // [2 3 4]  
    fmt.Println("data[:4]:", data[:4])      // [0 1 2 3]  
    fmt.Println("data[6:]:", data[6:])      // [6 7 8 9]  
    fmt.Println("data[:]:", data[:])        // [0 1 2 3 4 5 6 7 8 9]  
  
    // Срез с указанием ёмкости slice[start:end:cap]    limited := data[2:5:6]  
    fmt.Printf("Limited: %v, len: %d, cap: %d\n", limited, len(limited), cap(limited))  
    // Limited: [2 3 4], len: 3, cap: 4  
}
```

##### 🔸 Продвинутые операции среза
```go
func main() {
    original := []int{1, 2, 3, 4, 5, 6, 7, 8}

    // Получение каждого второго элемента
    var everySecond []int
    for i := 0; i < len(original); i += 2 {
        everySecond = append(everySecond, original[i])
    }
    fmt.Println("Every second:", everySecond) // [1 3 5 7]

    // Получение последних n элементов
    n := 3
    last := original[len(original)-n:]
    fmt.Println("Last 3:", last) // [6 7 8]

    // Удаление элемента из середины
    index := 3
    without := append(original[:index], original[index+1:]...)
    fmt.Println("Without index 3:", without) // [1 2 3 5 6 7 8]
}
```

> несколько срезов могут ссылаться на один базовый массив, нужно понимать что при изменении базового массива, изменения произойдут и в сегменте рекомендуется создавать сегменты с использованием make или литерала сегмента (вместо того, чтобы создать массив и применять к нему оператор сегмента). С make и литералами сегментов вам никогда не приходится иметь дела с базовым массивом.

##### 🔸 Append - добавление элементов
> функция append, получает сегмент и одно или несколько значений, которые присоединяются в конец сегмента, функция возвращает новый сегмент. Во избежание проблем с пересечением значение при добавление новых эл. желательно новый сегмент присваивать той же переменой (как в примере выше) а не новой.


```go
func main() {  
    var slice []int  
    // Добавление одного элемента  
    slice = append(slice, 1)  
    fmt.Println("After append 1:", slice) // [1]  
  
    // Добавление нескольких элементов    
    slice = append(slice, 2, 3, 4)  
    fmt.Println("After append 2,3,4:", slice) // [1 2 3 4]  
  
    // Добавление другого слайса    
    more := []int{5, 6, 7}  
    slice = append(slice, more...)  
    fmt.Println("After append slice:", slice) // [1 2 3 4 5 6 7]
    

	// Изначально пустой слайс  
	slice := make([]int, 0, 2)  
	fmt.Printf("Initial: len=%d, cap=%d\n", len(slice), cap(slice)) // len=0, cap=2  
  
	// Добавляем элементы в пределах ёмкости  
	slice = append(slice, 1)  
	fmt.Printf("After append 1: len=%d, cap=%d, slice=%v\n", len(slice), cap(slice), slice) // len=1, cap=2, slice=[1]  
  
	slice = append(slice, 2)  
	fmt.Printf("After append 2: len=%d, cap=%d, slice=%v\n", len(slice), cap(slice), slice) // len=2, cap=2, slice=[1 2]  
  
	// Превышаем ёмкость - произойдёт реаллокация  
	slice = append(slice, 3)  
	fmt.Printf("After append 3: len=%d, cap=%d, slice=%v\n", len(slice), cap(slice), slice) // len=3, cap=4, slice=[1 2 3]  
}
```

##### 🔸 Copy - копирование слайсов
```go
func main() {
    source := []int{1, 2, 3, 4, 5}
    
    // Создание слайса для копирования
    dest := make([]int, len(source))
    
    // Копирование
    copied := copy(dest, source)
    fmt.Printf("Copied %d elements: %v\n", copied, dest) // Copied 5 elements: [1 2 3 4 5]

    // Копирование в слайс меньшего размера
    small := make([]int, 3)
    copied = copy(small, source)
    fmt.Printf("Copied %d elements: %v\n", copied, small) // Copied 3 elements: [1 2 3]

    // Копирование в слайс большего размера
    large := make([]int, 8)
    copied = copy(large, source)
    fmt.Printf("Copied %d elements: %v\n", copied, large) // Copied 5 elements: [1 2 3 4 5 0 0 0]
    
    // ===================================
    // Продвинутое использование copy
    
    data := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}  
  
	// Сдвиг элементов влево (удаление элемента)  
	indexToRemove := 3  
	copy(data[indexToRemove:], data[indexToRemove+1:])  
	data = data[:len(data)-1]  
	fmt.Println("After removal:", data) // [1 2 3 5 6 7 8 9 10]  
  
	// Вставка элемента в середину  
	insertIndex := 2  
	value := 99  
	data = append(data, 0)  
	copy(data[insertIndex+1:], data[insertIndex:])  
	data[insertIndex] = value  
	fmt.Println("After insertion:", data) // [1 2 99 3 5 6 7 8 9 10]
}
```
---
#### 🔹 Многомерные слайсы
##### 🔸 Двумерные слайсы
```go
func main() {
    // Создание двумерного слайса
    matrix := make([][]int, 3)
    for i := range matrix {
        matrix[i] = make([]int, 4)
    }

    // Заполнение данными
    for i := 0; i < 3; i++ {
        for j := 0; j < 4; j++ {
            matrix[i][j] = i*4 + j + 1
        }
    }

    fmt.Println("Matrix:")
    for _, row := range matrix {
        fmt.Println(row)
    }
    // [1 2 3 4]
    // [5 6 7 8]
    // [9 10 11 12]
}
```

##### 🔸 Неровные (jagged) слайсы
```go
func main() {
    // Создание слайса слайсов разной длины
    triangle := make([][]int, 4)
    
    for i := range triangle {
        triangle[i] = make([]int, i+1)
        for j := range triangle[i] {
            triangle[i][j] = i + j + 1
        }
    }

    fmt.Println("Triangle:")
    for _, row := range triangle {
        fmt.Println(row)
    }
    // [1]
    // [1 2]
    // [2 3 4]
    // [3 4 5 6]
}
```
---
#### 🔹 Итерация по слайсам

```go
func main() {
    numbers := []int{10, 20, 30, 40, 50}

    // Обычный for цикл
    fmt.Println("Regular for loop:")
    for i := 0; i < len(numbers); i++ {
        fmt.Printf("Index %d: %d\n", i, numbers[i])
    }

    // Range с индексом и значением
    fmt.Println("\nRange with index and value:")
    for index, value := range numbers {
        fmt.Printf("Index %d: %d\n", index, value)
    }

    // Range только со значениями
    fmt.Println("\nRange with values only:")
    for _, value := range numbers {
        fmt.Printf("Value: %d\n", value)
    }

    // Range только с индексами
    fmt.Println("\nRange with indices only:")
    for index := range numbers {
        fmt.Printf("Index: %d\n", index)
    }

    // Обратная итерация
    fmt.Println("\nReverse iteration:")
    for i := len(numbers) - 1; i >= 0; i-- {
        fmt.Printf("Index %d: %d\n", i, numbers[i])
    }
}
```
---
#### 🔹 Передача слайсов в функции
```go
func modifySlice(s []int) {
    if len(s) > 0 {
        s[0] = 999
    }
    s = append(s, 100) // Это не повлияет на оригинальный слайс
}

func modifySlicePointer(s *[]int) {
    if len(*s) > 0 {
        (*s)[0] = 888
    }
    *s = append(*s, 200) // Это изменит оригинальный слайс
}

func main() {
    original := []int{1, 2, 3}
    fmt.Println("Original:", original) // [1 2 3]

    modifySlice(original)
    fmt.Println("After modifySlice:", original) // [999 2 3]

    modifySlicePointer(&original)
    fmt.Println("After modifySlicePointer:", original) // [888 2 3 200]
}
```
---
#### 🔹 Практические примеры
##### 🔸 Фильтрация слайса
```go
func filter(slice []int, predicate func(int) bool) []int {
    var result []int
    for _, value := range slice {
        if predicate(value) {
            result = append(result, value)
        }
    }
    return result
}

func main() {
    numbers := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

    // Фильтрация чётных чисел
    evens := filter(numbers, func(n int) bool {
        return n%2 == 0
    })
    fmt.Println("Even numbers:", evens) // [2 4 6 8 10]

    // Фильтрация чисел больше 5
    greaterThan5 := filter(numbers, func(n int) bool {
        return n > 5
    })
    fmt.Println("Greater than 5:", greaterThan5) // [6 7 8 9 10]
}
```

##### 🔸 Поиск в слайсе
```go
func find(slice []string, target string) int {
    for i, value := range slice {
        if value == target {
            return i
        }
    }
    return -1
}

func contains(slice []string, target string) bool {
    return find(slice, target) != -1
}

func findAll(slice []string, target string) []int {
    var indices []int
    for i, value := range slice {
        if value == target {
            indices = append(indices, i)
        }
    }
    return indices
}

func main() {
    words := []string{"go", "java", "python", "go", "javascript", "go"}

    index := find(words, "python")
    fmt.Println("Index of 'python':", index) // 2

    hasGo := contains(words, "go")
    fmt.Println("Contains 'go':", hasGo) // true

    allGo := findAll(words, "go")
    fmt.Println("All indices of 'go':", allGo) // [0 3 5]
}
```
##### 🔸 Сортировка слайсов
```go
import (
    "fmt"
    "sort"
)

type Person struct {
    Name string
    Age  int
}

func main() {
    // Сортировка чисел
    numbers := []int{64, 34, 25, 12, 22, 11, 90}
    sort.Ints(numbers)
    fmt.Println("Sorted numbers:", numbers) // [11 12 22 25 34 64 90]

    // Сортировка строк
    words := []string{"banana", "apple", "cherry", "date"}
    sort.Strings(words)
    fmt.Println("Sorted words:", words) // [apple banana cherry date]

    // Кастомная сортировка
    people := []Person{
        {"Alice", 30},
        {"Bob", 25},
        {"Charlie", 35},
    }

    // Сортировка по возрасту
    sort.Slice(people, func(i, j int) bool {
        return people[i].Age < people[j].Age
    })
    fmt.Println("Sorted by age:", people) // [{Bob 25} {Alice 30} {Charlie 35}]
}
```
##### 🔸 Группировка элементов
```go
func groupBy(slice []string, keyFunc func(string) string) map[string][]string {
    groups := make(map[string][]string)
    
    for _, item := range slice {
        key := keyFunc(item)
        groups[key] = append(groups[key], item)
    }
    
    return groups
}

func main() {
    words := []string{"apple", "banana", "apricot", "blueberry", "cherry", "coconut"}

    // Группировка по первой букве
    byFirstLetter := groupBy(words, func(word string) string {
        return string(word[0])
    })

    fmt.Println("Grouped by first letter:")
    for letter, group := range byFirstLetter {
        fmt.Printf("%s: %v\n", letter, group)
    }
    // a: [apple apricot]
    // b: [banana blueberry]
    // c: [cherry coconut]
}
```
##### 🔸 Работа со слайсами байтов
```go
func main() {
    // Преобразование строки в слайс байтов
    text := "Hello, мир!"
    bytes := []byte(text)
    fmt.Printf("Bytes: %v\n", bytes)

    // Преобразование обратно в строку
    restored := string(bytes)
    fmt.Println("Restored:", restored)

    // Работа с UTF-8
    runes := []rune(text)
    fmt.Printf("Runes: %v\n", runes)
    fmt.Println("Rune string:", string(runes))
}
```
---
#### 🔹 Оптимизация работы со слайсами
##### 🔸 Предварительное выделение памяти
```go
func inefficientAppend() []int {
    var result []int
    for i := 0; i < 10000; i++ {
        result = append(result, i) // Множественные реаллокации
    }
    return result
}

func efficientAppend() []int {
    result := make([]int, 0, 10000) // Предварительное выделение ёмкости
    for i := 0; i < 10000; i++ {
        result = append(result, i) // Без реаллокаций
    }
    return result
}

func main() {
    // Измерение времени выполнения
    import "time"
    
    start := time.Now()
    inefficientAppend()
    fmt.Printf("Inefficient took: %v\n", time.Since(start))

    start = time.Now()
    efficientAppend()
    fmt.Printf("Efficient took: %v\n", time.Since(start))
}
```
##### 🔸 Избежание утечек памяти
```go
func potentialMemoryLeak() []int {
    // Большой слайс
    bigSlice := make([]int, 1000000)
    
    // Возвращаем только небольшую часть
    // Но весь базовый массив остаётся в памяти!
    return bigSlice[:10]
}

func avoidMemoryLeak() []int {
    bigSlice := make([]int, 1000000)
    
    // Создаём новый слайс с копированием
    small := make([]int, 10)
    copy(small, bigSlice[:10])
    
    return small // Большой слайс может быть освобождён GC
}
```

---
### 🔹 MAP
 > это неупорядоченная коллекция пар вида ключ-значение (ассоциативный массив). У массивов и сегментов индексами могут быть только целые числа. Для ключей карты можно выбрать практически любой тип

Как и в случае с сегментами, объявление переменной-карты не приводит к автоматическому созданию карты, для этого необходимо вызвать функцию `make`. Если объявить переменную для карты, но не присвоить ей значение, она будет содержать `nil`, соответсвенно в нее нельзя будет добавить ключ со значение

##### 🔸 Создание и инициализация
```go
package main

import "fmt"

func main() {
    // 1. Объявление пустой map
    var ages map[string]int
    fmt.Println("Empty map:", ages)        // map[]
    fmt.Println("Is nil:", ages == nil)    // true

    // 2. Создание с помощью make
    scores := make(map[string]int)
    fmt.Println("Made map:", scores)       // map[]

    // 3. Инициализация литералом
    colors := map[string]string{
        "red":   "#FF0000",
        "green": "#00FF00",
        "blue":  "#0000FF",
    }
    fmt.Println("Initialized map:", colors)

    // 4. Создание с начальной ёмкостью (оптимизация)
    cache := make(map[string]int, 100)
    fmt.Println("Cache created with capacity")
}
```

##### 🔸 Основные операции
```go
func main() {
    // Создание map
    students := make(map[string]int)

    // Добавление элементов
    students["Alice"] = 85
    students["Bob"] = 92
    students["Charlie"] = 78
    
    fmt.Println("Students:", students) // map[Alice:85 Bob:92 Charlie:78]

    // Получение значения
    aliceScore := students["Alice"]
    fmt.Println("Alice's score:", aliceScore) // 85

    // Проверка существования ключа
    score, exists := students["David"]
    if exists {
        fmt.Println("David's score:", score)
    } else {
        fmt.Println("David not found") // David not found
    }

    // Обновление значения
    students["Alice"] = 90
    fmt.Println("Updated Alice:", students["Alice"]) // 90

    // Удаление элемента
    delete(students, "Bob")
    fmt.Println("After deletion:", students) // map[Alice:90 Charlie:78]

    // Длина map
    fmt.Println("Number of students:", len(students)) // 2
}
```

##### 🔸 Допустимые типы ключей
```go
func main() {
    // Строковые ключи
    stringMap := map[string]int{"key": 42}

    // Числовые ключи
    intMap := map[int]string{1: "one", 2: "two"}
    floatMap := map[float64]bool{3.14: true, 2.71: false}

    // Булевы ключи
    boolMap := map[bool]string{true: "yes", false: "no"}

    // Массивы как ключи (но не слайсы!)
    arrayMap := map[[3]int]string{
        {1, 2, 3}: "first",
        {4, 5, 6}: "second",
    }

    // Структуры как ключи (если все поля сравнимы)
    type Point struct {
        X, Y int
    }
    pointMap := map[Point]string{
        {0, 0}: "origin",
        {1, 1}: "diagonal",
    }

    fmt.Println("Maps created successfully")

    // Недопустимые типы ключей (вызовут ошибку компиляции):
    // sliceMap := map[[]int]string{}     // слайсы
    // mapMap := map[map[int]int]string{} // вложенные map
    // funcMap := map[func()]string{}     // функции
}
```

##### 🔸 Различные типы значений
```go
func main() {
    // Map со слайсами как значениями
    groups := map[string][]string{
        "fruits":     {"apple", "banana", "orange"},
        "vegetables": {"carrot", "broccoli", "spinach"},
    }

    // Map с map как значениями
    nested := map[string]map[string]int{
        "math":    {"alice": 95, "bob": 87},
        "english": {"alice": 92, "bob": 89},
    }

    // Map со структурами как значениями
    type Person struct {
        Age    int
        City   string
        Salary float64
    }

    employees := map[int]Person{
        1001: {Age: 30, City: "Moscow", Salary: 75000.0},
        1002: {Age: 25, City: "SPb", Salary: 65000.0},
    }

    fmt.Println("Groups:", groups)
    fmt.Println("Nested:", nested)
    fmt.Println("Employees:", employees)
}
```
---
#### 🔹 Итерация по Maps

##### 🔸 Различные способы итерации
```go
func main() {
    inventory := map[string]int{
        "apples":  50,
        "bananas": 30,
        "oranges": 25,
        "grapes":  40,
    }

    // Итерация по ключам и значениям
    fmt.Println("Inventory:")
    for item, quantity := range inventory {
        fmt.Printf("%s: %d\n", item, quantity)
    }

    // Итерация только по ключам
    fmt.Println("\nItems:")
    for item := range inventory {
        fmt.Printf("- %s\n", item)
    }

    // Итерация только по значениям
    fmt.Println("\nQuantities:")
    for _, quantity := range inventory {
        fmt.Printf("Quantity: %d\n", quantity)
    }

    // Примечание: порядок итерации НЕ гарантирован!
    // Go специально рандомизирует порядок для предотвращения
    // зависимости от порядка элементов
}
```

##### 🔸 Сортированная итерация
```go
import (
    "fmt"
    "sort"
)

func main() {
    data := map[string]int{
        "charlie": 3,
        "alice":   1,
        "bob":     2,
        "david":   4,
    }

    // Сортировка по ключам
    keys := make([]string, 0, len(data))
    for key := range data {
        keys = append(keys, key)
    }
    sort.Strings(keys)

    fmt.Println("Sorted by keys:")
    for _, key := range keys {
        fmt.Printf("%s: %d\n", key, data[key])
    }

    // Сортировка по значениям
    type KeyValue struct {
        Key   string
        Value int
    }

    var pairs []KeyValue
    for key, value := range data {
        pairs = append(pairs, KeyValue{key, value})
    }

    sort.Slice(pairs, func(i, j int) bool {
        return pairs[i].Value < pairs[j].Value
    })

    fmt.Println("\nSorted by values:")
    for _, pair := range pairs {
        fmt.Printf("%s: %d\n", pair.Key, pair.Value)
    }
}
```
---
#### 🔹 Функции для работы с Maps
##### 🔸 Проверка наличия ключа
```go
func keyExists(m map[string]int, key string) bool {
    _, exists := m[key]
    return exists
}

func safeGet(m map[string]int, key string, defaultValue int) int {
    if value, exists := m[key]; exists {
        return value
    }
    return defaultValue
}

func main() {
    scores := map[string]int{
        "alice": 95,
        "bob":   87,
    }

    // Проверка существования
    if keyExists(scores, "alice") {
        fmt.Println("Alice exists in scores")
    }

    // Безопасное получение с значением по умолчанию
    charlieScore := safeGet(scores, "charlie", 0)
    fmt.Printf("Charlie's score: %d\n", charlieScore) // 0
}
```

##### 🔸 Слияние Maps
```go
func mergeMaps(dest, src map[string]int) {
    for key, value := range src {
        dest[key] = value
    }
}

func mergeWithConflictResolution(dest, src map[string]int, resolver func(key string, oldVal, newVal int) int) {
    for key, newValue := range src {
        if oldValue, exists := dest[key]; exists {
            dest[key] = resolver(key, oldValue, newValue)
        } else {
            dest[key] = newValue
        }
    }
}

func main() {
    map1 := map[string]int{"a": 1, "b": 2}
    map2 := map[string]int{"b": 3, "c": 4}

    // Простое слияние
    result1 := make(map[string]int)
    mergeMaps(result1, map1)
    mergeMaps(result1, map2)
    fmt.Println("Simple merge:", result1) // map[a:1 b:3 c:4]

    // Слияние с разрешением конфликтов (сумма значений)
    result2 := make(map[string]int)
    mergeMaps(result2, map1)
    mergeWithConflictResolution(result2, map2, func(key string, oldVal, newVal int) int {
        return oldVal + newVal
    })
    fmt.Println("Merge with sum:", result2) // map[a:1 b:5 c:4]
}
```
---
#### 🔹 Передача Maps в функции
##### 🔸 Maps передаются по ссылке
```go
func modifyMap(m map[string]int) {
    m["new_key"] = 100
    m["existing"] = 999
}

func reassignMap(m map[string]int) {
    // Это не изменит оригинальную map!
    m = make(map[string]int)
    m["reassigned"] = 1
}

func reassignMapPointer(m *map[string]int) {
    // Это изменит оригинальную map
    *m = make(map[string]int)
    (*m)["reassigned"] = 1
}

func main() {
    original := map[string]int{"existing": 42}
    fmt.Println("Original:", original) // map[existing:42]

    modifyMap(original)
    fmt.Println("After modify:", original) // map[existing:999 new_key:100]

    reassignMap(original)
    fmt.Println("After reassign:", original) // map[existing:999 new_key:100]

    reassignMapPointer(&original)
    fmt.Println("After pointer reassign:", original) // map[reassigned:1]
}
```
---
#### 🔹 Многомерные и вложенные Maps
##### 🔸 Двумерные Maps
```go
func main() {
    // Map из Maps - матрица
    matrix := make(map[int]map[int]string)

    // Инициализация внутренних map
    for i := 0; i < 3; i++ {
        matrix[i] = make(map[int]string)
        for j := 0; j < 3; j++ {
            matrix[i][j] = fmt.Sprintf("(%d,%d)", i, j)
        }
    }

    // Вывод матрицы
    for i := 0; i < 3; i++ {
        for j := 0; j < 3; j++ {
            fmt.Printf("%s ", matrix[i][j])
        }
        fmt.Println()
    }

    // Безопасный доступ к вложенной map
    if row, exists := matrix[1]; exists {
        if value, exists := row[2]; exists {
            fmt.Printf("matrix[1][2] = %s\n", value)
        }
    }
}
```
---
#### 🔹 Практические примеры
##### 🔸 Система пользователей
```go
type User struct {
    Name     string
    Email    string
    Age      int
    IsActive bool
}

type Permission struct {
    Read   bool
    Write  bool
    Delete bool
}

func main() {
    // Пользователи
    users := map[int]User{
        1: {Name: "Alice", Email: "alice@example.com", Age: 30, IsActive: true},
        2: {Name: "Bob", Email: "bob@example.com", Age: 25, IsActive: true},
        3: {Name: "Charlie", Email: "charlie@example.com", Age: 35, IsActive: false},
    }

    // Разрешения пользователей
    permissions := map[int]map[string]Permission{
        1: {
            "posts":    {Read: true, Write: true, Delete: false},
            "comments": {Read: true, Write: true, Delete: true},
        },
        2: {
            "posts":    {Read: true, Write: false, Delete: false},
            "comments": {Read: true, Write: true, Delete: false},
        },
    }

    // Проверка разрешений
    userID := 1
    resource := "posts"

    if user, userExists := users[userID]; userExists && user.IsActive {
        if userPerms, permExists := permissions[userID]; permExists {
            if perm, resourceExists := userPerms[resource]; resourceExists {
                fmt.Printf("User %s permissions for %s: Read=%t, Write=%t, Delete=%t\n",
                    user.Name, resource, perm.Read, perm.Write, perm.Delete)
            }
        }
    }
}
```

##### 🔸 Группировка данных
```go
type Student struct {
    Name  string
    Grade string
    Score int
}

func groupByGrade(students []Student) map[string][]Student {
    groups := make(map[string][]Student)
    for _, student := range students {
        groups[student.Grade] = append(groups[student.Grade], student)
    }
    return groups
}

func main() {
    students := []Student{
        {"Alice", "A", 95},
        {"Bob", "B", 87},
        {"Charlie", "A", 92},
        {"David", "C", 76},
        {"Eve", "B", 89},
    }

    grouped := groupByGrade(students)
    
    for grade, group := range grouped {
        fmt.Printf("Grade %s:\n", grade)
        for _, student := range group {
            fmt.Printf("  %s: %d\n", student.Name, student.Score)
        }
    }
}
```

##### 🔸 Кеширование результатов
```go
import (
    "fmt"
    "time"
)

// Простой кеш с TTL
type CacheItem struct {
    Value     interface{}
    ExpiresAt time.Time
}

type Cache struct {
    items map[string]CacheItem
}

func NewCache() *Cache {
    return &Cache{
        items: make(map[string]CacheItem),
    }
}

func (c *Cache) Set(key string, value interface{}, ttl time.Duration) {
    c.items[key] = CacheItem{
        Value:     value,
        ExpiresAt: time.Now().Add(ttl),
    }
}

func (c *Cache) Get(key string) (interface{}, bool) {
    item, exists := c.items[key]
    if !exists {
        return nil, false
    }
    
    if time.Now().After(item.ExpiresAt) {
        delete(c.items, key)
        return nil, false
    }
    
    return item.Value, true
}

func (c *Cache) Cleanup() {
    now := time.Now()
    for key, item := range c.items {
        if now.After(item.ExpiresAt) {
            delete(c.items, key)
        }
    }
}

func main() {
    cache := NewCache()
    
    // Сохранение в кеше
    cache.Set("user:123", "Alice", 5*time.Second)
    cache.Set("session:abc", "active", 10*time.Second)
    
    // Получение из кеша
    if value, found := cache.Get("user:123"); found {
        fmt.Printf("Found user: %s\n", value)
    }
    
    // Ожидание истечения TTL
    time.Sleep(6 * time.Second)
    
    if _, found := cache.Get("user:123"); !found {
        fmt.Println("User cache expired")
    }
}
```

##### 🔸 Индексирование данных
```go
type Product struct {
    ID       int
    Name     string
    Category string
    Price    float64
    Tags     []string
}

type ProductIndex struct {
    byID       map[int]Product
    byCategory map[string][]Product
    byTag      map[string][]Product
}

func NewProductIndex() *ProductIndex {
    return &ProductIndex{
        byID:       make(map[int]Product),
        byCategory: make(map[string][]Product),
        byTag:      make(map[string][]Product),
    }
}

func (idx *ProductIndex) Add(product Product) {
    // Индекс по ID
    idx.byID[product.ID] = product
    
    // Индекс по категории
    idx.byCategory[product.Category] = append(idx.byCategory[product.Category], product)
    
    // Индекс по тегам
    for _, tag := range product.Tags {
        idx.byTag[tag] = append(idx.byTag[tag], product)
    }
}

func (idx *ProductIndex) FindByID(id int) (Product, bool) {
    product, exists := idx.byID[id]
    return product, exists
}

func (idx *ProductIndex) FindByCategory(category string) []Product {
    return idx.byCategory[category]
}

func (idx *ProductIndex) FindByTag(tag string) []Product {
    return idx.byTag[tag]
}

func main() {
    index := NewProductIndex()
    
    products := []Product{
        {1, "Laptop", "Electronics", 999.99, []string{"computer", "portable", "work"}},
        {2, "Mouse", "Electronics", 29.99, []string{"computer", "input", "work"}},
        {3, "Coffee", "Food", 12.99, []string{"drink", "morning", "energy"}},
    }
    
    for _, product := range products {
        index.Add(product)
    }
    
    // Поиск по различным индексам
    if product, found := index.FindByID(1); found {
        fmt.Printf("Product 1: %s\n", product.Name)
    }
    
    electronics := index.FindByCategory("Electronics")
    fmt.Printf("Electronics: %d items\n", len(electronics))
    
    workItems := index.FindByTag("work")
    fmt.Printf("Work items: %d items\n", len(workItems))
}
```
---

#### 🔹 Производительность и оптимизация
##### 🔸 Предварительное выделение памяти
```go
import (
    "fmt"
    "time"
)

func benchmarkMapCreation() {
    const size = 100000

    // Без предварительного выделения
    start := time.Now()
    map1 := make(map[int]string)
    for i := 0; i < size; i++ {
        map1[i] = fmt.Sprintf("value_%d", i)
    }
    duration1 := time.Since(start)

    // С предварительным выделением
    start = time.Now()
    map2 := make(map[int]string, size)
    for i := 0; i < size; i++ {
        map2[i] = fmt.Sprintf("value_%d", i)
    }
    duration2 := time.Since(start)

    fmt.Printf("Without preallocation: %v\n", duration1)
    fmt.Printf("With preallocation: %v\n", duration2)
    fmt.Printf("Improvement: %.2fx\n", float64(duration1)/float64(duration2))
}
```

##### 🔸 Пулы Maps для переиспользования
```go
import "sync"

var mapPool = sync.Pool{
    New: func() interface{} {
        return make(map[string]int, 100)
    },
}

func usePooledMap() map[string]int {
    // Получение map из пула
    m := mapPool.Get().(map[string]int)
    
    // Очистка map (важно!)
    for k := range m {
        delete(m, k)
    }
    
    // Использование map
    m["key1"] = 1
    m["key2"] = 2
    
    return m
}

func returnToPool(m map[string]int) {
    // Возврат в пул
    mapPool.Put(m)
}

func main() {
    m := usePooledMap()
    fmt.Println("Used map:", m)
    returnToPool(m)
}
```
---
