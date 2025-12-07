#go


#### 🔹 Основы структур (struct)

> Структура представляет собой значение, которое строится из других значений разных типов

> Определения типов позволяют создавать собственные типы, они создают новый определяемый тип на основе базового типа.

 > В основном типы (`type`) определяются на уровне пакета, чтоб область видимости была глобальной, чтоб типы были экспортируемые их нужно писать с большой буквы, но также нужно и поля, которые мы хотим экспортировать, писать с большой. Типы можно указывать как в параметрах функции, так и в возвращаемых значениях функции
 
 > ==Кл. слово New==
   Go поддерживает ключевое слово new, которое позволяет размещать в памяти новые объекты. Однако, new возвращает указатель, это нужно помнить. В переменной `i` находится ссылка на выделеную область памяти, в которую можно поместить `int64`
   `i := new(int64)`
   ==Основное различие между `new` и `make` состоит в том==, что переменные, созданные с помощью `make`, правильно инициализируются, а не только обнуляется выделенная для них память. Кроме того, make можно применять только к хеш-таблицам, каналам и срезам, и эта функция не возвращает адрес памяти, то есть make не возвращает указатель
##### 🔸 Определение и использование структур
```go
package main

import "fmt"

// Простая структура
type Person struct {
    Name string
    Age  int
}

// Структура с различными типами полей
type Employee struct {
    ID       int
    Name     string
    Email    string
    Salary   float64
    IsActive bool
}

func main() {
    // Создание экземпляров структуры
    
    // 1. Литерал структуры с именованными полями
    p1 := Person{
        Name: "Иван",
        Age:  30,
    }
    
    // 2. Литерал структуры с позиционными полями
    p2 := Person{"Мария", 25}
    
    // 3. Пустая структура (нулевые значения)
    var p3 Person
    
    // 4. С помощью new (возвращает указатель)
    p4 := new(Person)
    p4.Name = "Петр"
    p4.Age = 40
    
    fmt.Printf("p1: %+v\n", p1) // p1: {Name:Иван Age:30}
    fmt.Printf("p2: %+v\n", p2) // p2: {Name:Мария Age:25}
    fmt.Printf("p3: %+v\n", p3) // p3: {Name: Age:0}
    fmt.Printf("p4: %+v\n", *p4) // p4: {Name:Петр Age:40}
}
```
##### 🔸 Доступ к полям и методы
```go
type Rectangle struct {
    Width  float64
    Height float64
}

// Метод с получателем по значению
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Метод с получателем по ссылке
func (r *Rectangle) Scale(factor float64) {
    r.Width *= factor
    r.Height *= factor
}

// Метод со строковым представлением
func (r Rectangle) String() string {
    return fmt.Sprintf("Rectangle{%.2f x %.2f}", r.Width, r.Height)
}

func main() {
    rect := Rectangle{Width: 10.0, Height: 5.0}
    
    fmt.Printf("Прямоугольник: %v\n", rect)
    fmt.Printf("Площадь: %.2f\n", rect.Area())
    
    // Изменение через метод-указатель
    rect.Scale(2.0)
    fmt.Printf("После масштабирования: %v\n", rect)
    fmt.Printf("Новая площадь: %.2f\n", rect.Area())
}
```
##### 🔸 Встраивание структур
```go
type Address struct {
    Street   string
    City     string
    Country  string
    PostCode string
}

func (a Address) FullAddress() string {
    return fmt.Sprintf("%s, %s, %s %s", a.Street, a.City, a.Country, a.PostCode)
}

type Person struct {
    Name  string
    Age   int
    Email string
    
    // Встроенная структура (анонимное поле)
    Address
}

type Employee struct {
    Person   // Встроенная структура
    ID       int
    Position string
    Salary   float64
}

func main() {
    emp := Employee{
        Person: Person{
            Name:  "Анна Петрова",
            Age:   28,
            Email: "anna@example.com",
            Address: Address{
                Street:   "Ленина 123",
                City:     "Москва",
                Country:  "Россия",
                PostCode: "101000",
            },
        },
        ID:       1001,
        Position: "Разработчик",
        Salary:   85000.0,
    }
    
    // Прямой доступ к полям встроенных структур
    fmt.Println("Имя:", emp.Name)           // из Person
    fmt.Println("Город:", emp.City)         // из Address через Person
    fmt.Println("Должность:", emp.Position) // собственное поле
    
    // Вызов методов встроенных типов
    fmt.Println("Адрес:", emp.FullAddress()) // метод из Address
    
    // Явное обращение к встроенным типам
    fmt.Printf("Полная информация:\n%+v\n", emp)
}
```
##### 🔸 Конфликты имён при встраивании
```go
type A struct {
    Name string
    Value int
}

func (a A) Method() string {
    return "Method from A"
}

type B struct {
    Name  string
    Count int
}

func (b B) Method() string {
    return "Method from B"
}

type Combined struct {
    A
    B
    
    // Собственное поле с тем же именем
    Name string
}

func (c Combined) Method() string {
    return "Method from Combined"
}

func main() {
    c := Combined{
        A:    A{Name: "A_Name", Value: 1},
        B:    B{Name: "B_Name", Count: 2},
        Name: "Combined_Name",
    }
    
    // Доступ к полям
    fmt.Println("c.Name:", c.Name)       // Combined_Name (собственное поле)
    fmt.Println("c.A.Name:", c.A.Name)   // A_Name (явный доступ)
    fmt.Println("c.B.Name:", c.B.Name)   // B_Name (явный доступ)
    
    // Вызов методов
    fmt.Println("c.Method():", c.Method())   // Method from Combined
    fmt.Println("c.A.Method():", c.A.Method()) // Method from A
    fmt.Println("c.B.Method():", c.B.Method()) // Method from B
    
    // Доступ к уникальным полям
    fmt.Println("c.Value:", c.Value)     // 1 (из A)
    fmt.Println("c.Count:", c.Count)     // 2 (из B)
}
```

##### 🔸 Анонимные поля структур
> Анонимные поля структур Go позволяет определять анонимные поля: поля структур, которые не имеют собственного имени, а обладают только типом. Анонимные поля упрощают доступ к внутренней структуре. При объявлении анонимного поля вы можете использовать имя типа поля так, как если бы оно было именем поля.

```go
type car struct {
	name string
	topSpeed float64
	available bool
}

type user struct{
	name: string
	email: string
	car
}

// обращатся так
user.сar.available = false

// но можно и так
user.available = false
```

##### 🔸 Указатели для структур

> предпочтительнее использовать указатели на структуру в параметрах функции, чем передовать саму структуру, так не создается копия структуры в память и соответсвенно экономится память, а функция просто получает адрес памяти этого единственного экземпляра, а затем может читать данные структуры, изменять их или делать что-то еще, и все это без создания дополнительной копии

```go
func c(name string) *someStruct {
	...
	// возвращаем  указатель на структуру, вместо самой структуры
	return &s
}

//  Обращение к полям структур по указателю
var bmw car
bmw.topSpeed = 222
// получаем указатель на значение структуры
var speed *car = &bmw
// получаем значение структуры по указателю
// а затем обращаемся к полю структуры
fmt.Println((*speed).topSpeed)

// или так
fmt.Println(speed.topSpeed)
```

==Пример инициализация типов по указателями без==
```go
type Car struct {
	name string
	speed int32
	available bool
}

func newCarPointer(name string, speed int32, available bool) *Car {
	return &Car{name, speed, available}
}

func newCar(name string, speed int32, available bool) Car {
	return Car{name, speed, available}
}

func main() {
	var cars []Car
	//bmw := Car{"bmw", 200, true}
	bmw := newCarPointer("bmw", 200, true)
	cars = append(cars, *bmw)
	ford := newCar("ford", 150, false)
	cars = append(cars, ford)

	fmt.Println("BMW")
	fmt.Println(bmw)	// здесь указатель на структуру
	fmt.Println("FORD")
	fmt.Println(ford)	// здесь сама структура
	fmt.Println("CARS")
	fmt.Println(cars)
}

```
---
#### 🔹 Теги структур

##### 🔸 Использование тегов для JSON
```go
import (
    "encoding/json"
    "fmt"
    "time"
)

type User struct {
    ID        int       `json:"id"`
    Name      string    `json:"name"`
    Email     string    `json:"email"`
    Password  string    `json:"-"`                    // исключить из JSON
    CreatedAt time.Time `json:"created_at,omitempty"` // omitempty - не включать если пустое
    UpdatedAt time.Time `json:"updated_at,omitempty"`
    IsActive  bool      `json:"is_active"`
    Metadata  map[string]interface{} `json:"metadata,omitempty"`
}

// Кастомная сериализация даты
type CustomTime time.Time

func (ct CustomTime) MarshalJSON() ([]byte, error) {
    return json.Marshal(time.Time(ct).Format("2006-01-02"))
}

func (ct *CustomTime) UnmarshalJSON(data []byte) error {
    var dateStr string
    if err := json.Unmarshal(data, &dateStr); err != nil {
        return err
    }
    
    parsed, err := time.Parse("2006-01-02", dateStr)
    if err != nil {
        return err
    }
    
    *ct = CustomTime(parsed)
    return nil
}

type Profile struct {
    UserID    int        `json:"user_id"`
    FirstName string     `json:"first_name"`
    LastName  string     `json:"last_name"`
    Birthday  CustomTime `json:"birthday"`
    Bio       string     `json:"bio,omitempty"`
}

func main() {
    user := User{
        ID:       123,
        Name:     "john_doe",
        Email:    "john@example.com",
        Password: "secret123", // не попадёт в JSON
        CreatedAt: time.Now(),
        IsActive:  true,
        Metadata: map[string]interface{}{
            "role":        "admin",
            "permissions": []string{"read", "write", "delete"},
        },
    }
    
    // Сериализация в JSON
    jsonData, err := json.MarshalIndent(user, "", "  ")
    if err != nil {
        fmt.Printf("Ошибка сериализации: %v\n", err)
        return
    }
    
    fmt.Println("JSON:")
    fmt.Println(string(jsonData))
    
    // Десериализация из JSON
    jsonStr := `{
        "id": 456,
        "name": "jane_doe",
        "email": "jane@example.com",
        "is_active": false,
        "metadata": {
            "role": "user"
        }
    }`
    
    var newUser User
    if err := json.Unmarshal([]byte(jsonStr), &newUser); err != nil {
        fmt.Printf("Ошибка десериализации: %v\n", err)
        return
    }
    
    fmt.Printf("\nДесериализованный пользователь: %+v\n", newUser)
}
```
##### 🔸 Теги для валидации и ORM
```go
// Пример с различными тегами
type Product struct {
    ID          int     `json:"id" db:"id" validate:"required"`
    Name        string  `json:"name" db:"name" validate:"required,min=1,max=100"`
    Description string  `json:"description" db:"description" validate:"max=500"`
    Price       float64 `json:"price" db:"price" validate:"required,gt=0"`
    CategoryID  int     `json:"category_id" db:"category_id" validate:"required"`
    IsActive    bool    `json:"is_active" db:"is_active"`
    
    // Связанные данные (не сохраняются в БД)
    Category *Category `json:"category,omitempty" db:"-"`
    Tags     []Tag     `json:"tags,omitempty" db:"-"`
}

type Category struct {
    ID   int    `json:"id" db:"id"`
    Name string `json:"name" db:"name"`
}

type Tag struct {
    ID   int    `json:"id" db:"id"`
    Name string `json:"name" db:"name"`
}

// Функция для получения тега поля
func getFieldTag(structType interface{}, fieldName, tagName string) string {
    t := reflect.TypeOf(structType)
    if t.Kind() == reflect.Ptr {
        t = t.Elem()
    }
    
    field, found := t.FieldByName(fieldName)
    if !found {
        return ""
    }
    
    return field.Tag.Get(tagName)
}

func main() {
    product := Product{
        ID:          1,
        Name:        "Ноутбук",
        Description: "Игровой ноутбук",
        Price:       75000.0,
        CategoryID:  1,
        IsActive:    true,
    }
    
    // Получение тегов
    fmt.Println("JSON тег для Name:", getFieldTag(product, "Name", "json"))
    fmt.Println("DB тег для Price:", getFieldTag(product, "Price", "db"))
    fmt.Println("Validation тег для Price:", getFieldTag(product, "Price", "validate"))
}
```
---
#### 🔹 Создание пользовательских типов с type

##### 🔸 Базовые пользовательские типы 
```go
// Создание нового типа на основе существующего
type UserID int
type Username string
type Temperature float64
type Status int

// Константы для Status
const (
    StatusInactive Status = iota
    StatusActive
    StatusSuspended
    StatusDeleted
)

// Методы для пользовательских типов
func (uid UserID) String() string {
    return fmt.Sprintf("User#%d", int(uid))
}

func (un Username) IsValid() bool {
    return len(string(un)) >= 3 && len(string(un)) <= 20
}

func (t Temperature) Celsius() float64 {
    return float64(t)
}

func (t Temperature) Fahrenheit() float64 {
    return float64(t)*9/5 + 32
}

func (t Temperature) Kelvin() float64 {
    return float64(t) + 273.15
}

func (s Status) String() string {
    switch s {
    case StatusInactive:
        return "Неактивен"
    case StatusActive:
        return "Активен"
    case StatusSuspended:
        return "Заблокирован"
    case StatusDeleted:
        return "Удалён"
    default:
        return "Неизвестен"
    }
}

func main() {
    var userID UserID = 12345
    var username Username = "john_doe"
    var temp Temperature = 25.5
    var status Status = StatusActive
    
    fmt.Printf("ID пользователя: %v\n", userID)
    fmt.Printf("Имя валидно: %v\n", username.IsValid())
    fmt.Printf("Температура: %.1f°C, %.1f°F, %.1fK\n", 
        temp.Celsius(), temp.Fahrenheit(), temp.Kelvin())
    fmt.Printf("Статус: %v\n", status)
    
    // Нужно явное приведение типов
    var regularInt int = int(userID)
    var regularString string = string(username)
    
    fmt.Printf("Обычное int: %d\n", regularInt)
    fmt.Printf("Обычная string: %s\n", regularString)
}
```
##### 🔸 Типы-псевдонимы
```go
// Псевдоним типа (alias) - не создаёт новый тип
type StringAlias = string
type IntAlias = int

// Новый тип - создаёт отдельный тип
type StringType string
type IntType int

func acceptString(s string) {
    fmt.Printf("Получена строка: %s\n", s)
}

func acceptStringType(s StringType) {
    fmt.Printf("Получен StringType: %s\n", s)
}

func main() {
    var alias StringAlias = "псевдоним"
    var newType StringType = "новый тип"
    var regular string = "обычная строка"
    
    // Псевдонимы полностью совместимы с оригинальным типом
    acceptString(alias)   // OK
    acceptString(regular) // OK
    
    // Новые типы требуют приведения
    acceptString(string(newType)) // OK - с приведением
    // acceptString(newType)      // Ошибка компиляции
    
    acceptStringType(newType)           // OK
    acceptStringType(StringType(regular)) // OK - с приведением
    // acceptStringType(regular)        // Ошибка компиляции
    
    // Проверка типов
    fmt.Printf("alias == regular: %v\n", alias == regular)
    fmt.Printf("newType == StringType(regular): %v\n", newType == StringType(regular))
}
```

##### 🔸 Методы определяемых типов

Определение метода очень похоже на определение функции, но у метода перед именем функции добавляется один дополнительный параметр, называемый параметром получателя. Имя параметра получателя в определении метода выбирается произвольно, но важен тип; метод, который вы определяете, связывается со всеми значениями этого типа.
Метода могут получать параметра, возвращаться значения работать с указателями, быть экспортируемыми или нет.
По общепринятым соглашениям разработчики Go обычно используют имя, состоящее из одной буквы — первой буквы имени типа получателя в нижнем регистре
В Go параметры получателей занимают место значений `self` или `this` из других языков программирования
==Метод нужно определять в том же пакете, где определяется тип==

 ```go
// определяем тип
type MyType string

// определяем метод для типа MyType
func (m MyType) sayHi() {
	fmt.Println("Hi")
}

// создаем значения
value := MyType("a MyType value")
// вызываем метод sayHi для этого значения
value.sayHi()
 ```

##### 🔸 Определяемые типы

Определяемые типы Go чаще всего используют структуры в качестве базовых типов, но вместо них также могут использоваться `int`, строки, логические значения или любой другой тип. Они помогают четко обозначить, для чего должно использоваться значение

```go
type Liters float64
type Gallons float64

func f (){
	// определяется перем. нужных типов
	var carFuel Gallons
	var busFuel Liters
	// преобразуют float64  внужный тип
	carFuel = Gallons(10.0)
	busFuel = Liters(240.0)

	fmt.Println(carFuel, busFuel)
}
// короткая запись
carFuel := Gallons(10.0)
busFuel := Liters(240.0)

// Если имеется переменная, использующая определяемый тип, вы не сможете присвоить ей значение другого определяемого типа, даже если другой тип имеет такой же базовый тип

carFuel = Liters(10.0)
busFuel = Gallons(240.0)

// но можно выполнить преобразование между типами имеющими один базовый тип

// так как данные типы несут сведения о значения
// мы нарушаем эти сведения
carFuel = Gallons(Liters(40.0))
busFuel = Liters(Gallons(63.0))

// коректнее будет так
carFuel = Gallons(Liters(40.0) * 0.264)
busFuel = Liters(Gallons(63.0) * 3.785)

// Определяемый тип поддерживает все те же операции, что и базовый тип, т.е. над int и float можно проводить арифметические операции. Но определяемые типы не могут использоваться в операциях вместе со значениями другого типа, даже если другой тип имеет такой же базовый тип

// это check
fmt.Println(Liters(1.2) + Liters(3.4))
fmt.Println(Gallons(1.2) == 1.2)

// это fail
fmt.Println(Liters(1.2) + Gallons(3.4))

// добавление метода
func (g Gallons) ToLiters() Liters {
	return Liters(g * 3.785)
}

carFuel := Gallons(10.0)
fmt.Println(carFuel.ToLiters())
```
---
#### 🔹 Сложные структуры данных

##### 🔸 Связанные структуры
```go
// Система пользователей и ролей
type Role struct {
    ID          int
    Name        string
    Description string
    Permissions []Permission
}

type Permission struct {
    ID       int
    Name     string
    Resource string
    Action   string
}

type User struct {
    ID        int
    Username  string
    Email     string
    Password  string
    Profile   *Profile
    Roles     []Role
    CreatedAt time.Time
    UpdatedAt time.Time
}

type Profile struct {
    UserID    int
    FirstName string
    LastName  string
    Avatar    string
    Bio       string
    Settings  UserSettings
}

type UserSettings struct {
    Theme       string
    Language    string
    Timezone    string
    Newsletters bool
}

// Методы для работы с пользователем
func (u *User) HasPermission(resource, action string) bool {
    for _, role := range u.Roles {
        for _, perm := range role.Permissions {
            if perm.Resource == resource && perm.Action == action {
                return true
            }
        }
    }
    return false
}

func (u *User) AddRole(role Role) {
    // Проверяем, нет ли уже такой роли
    for _, existingRole := range u.Roles {
        if existingRole.ID == role.ID {
            return // роль уже есть
        }
    }
    u.Roles = append(u.Roles, role)
    u.UpdatedAt = time.Now()
}

func (u *User) FullName() string {
    if u.Profile != nil {
        return fmt.Sprintf("%s %s", u.Profile.FirstName, u.Profile.LastName)
    }
    return u.Username
}

func main() {
    // Создание разрешений
    readPerm := Permission{ID: 1, Name: "read", Resource: "posts", Action: "read"}
    writePerm := Permission{ID: 2, Name: "write", Resource: "posts", Action: "write"}
    deletePerm := Permission{ID: 3, Name: "delete", Resource: "posts", Action: "delete"}
    
    // Создание ролей
    userRole := Role{
        ID:          1,
        Name:        "user",
        Description: "Обычный пользователь",
        Permissions: []Permission{readPerm},
    }
    
    editorRole := Role{
        ID:          2,
        Name:        "editor",
        Description: "Редактор контента",
        Permissions: []Permission{readPerm, writePerm},
    }
    
    adminRole := Role{
        ID:          3,
        Name:        "admin", 
        Description: "Администратор",
        Permissions: []Permission{readPerm, writePerm, deletePerm},
    }
    
    // Создание пользователя
    user := User{
        ID:       1,
        Username: "john_doe",
        Email:    "john@example.com",
        Password: "hashed_password",
        Profile: &Profile{
            UserID:    1,
            FirstName: "Иван",
            LastName:  "Иванов",
            Avatar:    "avatar.jpg",
            Bio:       "Разработчик",
            Settings: UserSettings{
                Theme:       "dark",
                Language:    "ru",
                Timezone:    "Europe/Moscow",
                Newsletters: true,
            },
        },
        Roles:     []Role{userRole},
        CreatedAt: time.Now(),
        UpdatedAt: time.Now(),
    }
    
    fmt.Printf("Пользователь: %s (%s)\n", user.FullName(), user.Username)
    fmt.Printf("Может читать посты: %v\n", user.HasPermission("posts", "read"))
    fmt.Printf("Может писать посты: %v\n", user.HasPermission("posts", "write"))
    
    // Добавляем роль редактора
    user.AddRole(editorRole)
    fmt.Printf("После добавления роли редактора:\n")
    fmt.Printf("Может писать посты: %v\n", user.HasPermission("posts", "write"))
    fmt.Printf("Может удалять посты: %v\n", user.HasPermission("posts", "delete"))
}
```

##### 🔸 Деревья и графы
```go
// Структура для дерева
type TreeNode struct {
    Value    int
    Children []*TreeNode
    Parent   *TreeNode
}

func NewTreeNode(value int) *TreeNode {
    return &TreeNode{Value: value}
}

func (node *TreeNode) AddChild(child *TreeNode) {
    child.Parent = node
    node.Children = append(node.Children, child)
}

func (node *TreeNode) IsLeaf() bool {
    return len(node.Children) == 0
}

func (node *TreeNode) IsRoot() bool {
    return node.Parent == nil
}

func (node *TreeNode) Depth() int {
    if node.Parent == nil {
        return 0
    }
    return node.Parent.Depth() + 1
}

// Обход дерева в глубину
func (node *TreeNode) DFS(visit func(*TreeNode)) {
    visit(node)
    for _, child := range node.Children {
        child.DFS(visit)
    }
}

// Обход дерева в ширину
func (node *TreeNode) BFS(visit func(*TreeNode)) {
    queue := []*TreeNode{node}
    
    for len(queue) > 0 {
        current := queue[0]
        queue = queue[1:]
        
        visit(current)
        
        for _, child := range current.Children {
            queue = append(queue, child)
        }
    }
}

// Структура для графа
type Graph struct {
    Vertices map[string]*Vertex
}

type Vertex struct {
    Name     string
    Edges    []*Edge
    Visited  bool
    Distance int
}

type Edge struct {
    To     *Vertex
    Weight int
}

func NewGraph() *Graph {
    return &Graph{Vertices: make(map[string]*Vertex)}
}

func (g *Graph) AddVertex(name string) {
    if _, exists := g.Vertices[name]; !exists {
        g.Vertices[name] = &Vertex{Name: name}
    }
}

func (g *Graph) AddEdge(from, to string, weight int) {
    g.AddVertex(from)
    g.AddVertex(to)
    
    fromVertex := g.Vertices[from]
    toVertex := g.Vertices[to]
    
    fromVertex.Edges = append(fromVertex.Edges, &Edge{To: toVertex, Weight: weight})
}

func (g *Graph) DFS(startName string, visit func(*Vertex)) {
    if start, exists := g.Vertices[startName]; exists {
        g.resetVisited()
        g.dfsRecursive(start, visit)
    }
}

func (g *Graph) resetVisited() {
    for _, vertex := range g.Vertices {
        vertex.Visited = false
    }
}

func (g *Graph) dfsRecursive(vertex *Vertex, visit func(*Vertex)) {
    vertex.Visited = true
    visit(vertex)
    
    for _, edge := range vertex.Edges {
        if !edge.To.Visited {
            g.dfsRecursive(edge.To, visit)
        }
    }
}

func main() {
    // Пример работы с деревом
    fmt.Println("=== ДЕРЕВО ===")
    root := NewTreeNode(1)
    child1 := NewTreeNode(2)
    child2 := NewTreeNode(3)
    child3 := NewTreeNode(4)
    
    root.AddChild(child1)
    root.AddChild(child2)
    child1.AddChild(child3)
    
    fmt.Println("Обход в глубину:")
    root.DFS(func(node *TreeNode) {
        fmt.Printf("Узел %d (глубина: %d)\n", node.Value, node.Depth())
    })
    
    fmt.Println("\nОбход в ширину:")
    root.BFS(func(node *TreeNode) {
        fmt.Printf("Узел %d (глубина: %d)\n", node.Value, node.Depth())
    })
    
    // Пример работы с графом
    fmt.Println("\n=== ГРАФ ===")
    graph := NewGraph()
    
    graph.AddEdge("A", "B", 1)
    graph.AddEdge("A", "C", 2)
    graph.AddEdge("B", "D", 3)
    graph.AddEdge("C", "D", 1)
    graph.AddEdge("D", "E", 2)
    
    fmt.Println("Обход графа в глубину из A:")
    graph.DFS("A", func(vertex *Vertex) {
        fmt.Printf("Вершина: %s\n", vertex.Name)
    })
}
```
---
#### 🔹 Рефлексия и типы
##### 🔸 Использование reflect для анализа структур
```go
import (
    "fmt"
    "reflect"
    "strings"
)

type Student struct {
    ID        int    `json:"id" validate:"required"`
    Name      string `json:"name" validate:"required,min=2"`
    Email     string `json:"email" validate:"email"`
    Age       int    `json:"age" validate:"min=0,max=150"`
    IsActive  bool   `json:"is_active"`
    Grades    []int  `json:"grades"`
}

// Функция для анализа структуры
func AnalyzeStruct(s interface{}) {
    v := reflect.ValueOf(s)
    t := reflect.TypeOf(s)
    
    // Если передан указатель, получаем элемент
    if v.Kind() == reflect.Ptr {
        v = v.Elem()
        t = t.Elem()
    }
    
    fmt.Printf("Анализ структуры: %s\n", t.Name())
    fmt.Printf("Количество полей: %d\n\n", v.NumField())
    
    for i := 0; i < v.NumField(); i++ {
        field := t.Field(i)
        value := v.Field(i)
        
        fmt.Printf("Поле %d:\n", i+1)
        fmt.Printf("  Имя: %s\n", field.Name)
        fmt.Printf("  Тип: %s\n", field.Type)
        fmt.Printf("  Значение: %v\n", value.Interface())
        fmt.Printf("  Можно изменить: %v\n", value.CanSet())
        
        // Анализ тегов
        if jsonTag := field.Tag.Get("json"); jsonTag != "" {
            fmt.Printf("  JSON тег: %s\n", jsonTag)
        }
        if validateTag := field.Tag.Get("validate"); validateTag != "" {
            fmt.Printf("  Validation тег: %s\n", validateTag)
        }
        
        fmt.Println()
    }
}

// Функция для копирования полей между структурами
func CopyFields(src, dst interface{}) error {
    srcVal := reflect.ValueOf(src)
    dstVal := reflect.ValueOf(dst)
    
    if srcVal.Kind() == reflect.Ptr {
        srcVal = srcVal.Elem()
    }
    if dstVal.Kind() != reflect.Ptr {
        return fmt.Errorf("dst должен быть указателем")
    }
    dstVal = dstVal.Elem()
    
    srcType := srcVal.Type()
    dstType := dstVal.Type()
    
    for i := 0; i < srcVal.NumField(); i++ {
        srcField := srcType.Field(i)
        srcFieldVal := srcVal.Field(i)
        
        // Ищем поле с таким же именем в dst
        if dstField, found := dstType.FieldByName(srcField.Name); found {
            dstFieldVal := dstVal.FieldByName(srcField.Name)
            
            // Проверяем, что типы совместимы и поле можно изменить
            if dstFieldVal.CanSet() && srcField.Type == dstField.Type {
                dstFieldVal.Set(srcFieldVal)
                fmt.Printf("Скопировано поле %s: %v\n", srcField.Name, srcFieldVal.Interface())
            }
        }
    }
    
    return nil
}

// Функция для создания экземпляра по имени типа
func CreateInstance(typeName string) interface{} {
    switch typeName {
    case "Student":
        return &Student{}
    default:
        return nil
    }
}

func main() {
    student := Student{
        ID:       123,
        Name:     "Иван Петров",
        Email:    "ivan@example.com",
        Age:      20,
        IsActive: true,
        Grades:   []int{5, 4, 5, 3, 4},
    }
    
    fmt.Println("=== АНАЛИЗ СТРУКТУРЫ ===")
    AnalyzeStruct(student)
    
    fmt.Println("=== КОПИРОВАНИЕ ПОЛЕЙ ===")
    var newStudent Student
    CopyFields(student, &newStudent)
    fmt.Printf("Новый студент: %+v\n", newStudent)
    
    fmt.Println("\n=== СОЗДАНИЕ ЭКЗЕМПЛЯРА ===")
    instance := CreateInstance("Student")
    if s, ok := instance.(*Student); ok {
        s.Name = "Создан через рефлексию"
        fmt.Printf("Созданный экземпляр: %+v\n", *s)
    }
}
```
---
#### 🔹 Лучшие практики

==Работа сос структурой==
- Желательно использовать встраивание структур, тем самым создавая композицию из них
- По возможности нужно стараться поля и методы делать закрытыми, и открывать то что нужно с наружи, если поля закрыты, то использовать конструктор для инициализации структуры
- Метод структуры без указателя работает с копией, нужно помнить об этом при обновлении полей в структуре
- Помнить про передачу по ссылке

##### 🔸 Инициализация и валидация
```go
import (
    "errors"
    "fmt"
    "regexp"
    "time"
)

type User struct {
    id        int       // приватное поле
    email     string    // приватное поле
    name      string    // приватное поле
    createdAt time.Time // приватное поле
}

// Конструктор с валидацией
func NewUser(id int, email, name string) (*User, error) {
    if id <= 0 {
        return nil, errors.New("id должен быть положительным")
    }
    
    if !isValidEmail(email) {
        return nil, errors.New("неверный формат email")
    }
    
    if len(name) < 2 {
        return nil, errors.New("имя должно содержать минимум 2 символа")
    }
    
    return &User{
        id:        id,
        email:     email,
        name:      name,
        createdAt: time.Now(),
    }, nil
}

// Геттеры для доступа к приватным полям
func (u *User) ID() int           { return u.id }
func (u *User) Email() string     { return u.email }
func (u *User) Name() string      { return u.name }
func (u *User) CreatedAt() time.Time { return u.createdAt }

// Сеттеры с валидацией
func (u *User) SetEmail(email string) error {
    if !isValidEmail(email) {
        return errors.New("неверный формат email")
    }
    u.email = email
    return nil
}

func (u *User) SetName(name string) error {
    if len(name) < 2 {
        return errors.New("имя должно содержать минимум 2 символа")
    }
    u.name = name
    return nil
}

func (u *User) String() string {
    return fmt.Sprintf("User{ID: %d, Name: %s, Email: %s, Created: %s}",
        u.id, u.name, u.email, u.createdAt.Format("2006-01-02 15:04:05"))
}

func isValidEmail(email string) bool {
    emailRegex := regexp.MustCompile(`^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$`)
    return emailRegex.MatchString(email)
}

// Builder паттерн для сложных структур
type ServerConfig struct {
    host         string
    port         int
    timeout      time.Duration
    maxConns     int
    enableSSL    bool
    sslCertPath  string
    sslKeyPath   string
    logLevel     string
}

type ServerConfigBuilder struct {
    config ServerConfig
}

func NewServerConfigBuilder() *ServerConfigBuilder {
    return &ServerConfigBuilder{
        config: ServerConfig{
            host:     "localhost",
            port:     8080,
            timeout:  30 * time.Second,
            maxConns: 100,
            logLevel: "info",
        },
    }
}

func (b *ServerConfigBuilder) Host(host string) *ServerConfigBuilder {
    b.config.host = host
    return b
}

func (b *ServerConfigBuilder) Port(port int) *ServerConfigBuilder {
    b.config.port = port
    return b
}

func (b *ServerConfigBuilder) Timeout(timeout time.Duration) *ServerConfigBuilder {
    b.config.timeout = timeout
    return b
}

func (b *ServerConfigBuilder) MaxConnections(maxConns int) *ServerConfigBuilder {
    b.config.maxConns = maxConns
    return b
}

func (b *ServerConfigBuilder) EnableSSL(certPath, keyPath string) *ServerConfigBuilder {
    b.config.enableSSL = true
    b.config.sslCertPath = certPath
    b.config.sslKeyPath = keyPath
    return b
}

func (b *ServerConfigBuilder) LogLevel(level string) *ServerConfigBuilder {
    b.config.logLevel = level
    return b
}

func (b *ServerConfigBuilder) Build() (*ServerConfig, error) {
    // Валидация конфигурации
    if b.config.port < 1 || b.config.port > 65535 {
        return nil, errors.New("порт должен быть в диапазоне 1-65535")
    }
    
    if b.config.enableSSL {
        if b.config.sslCertPath == "" || b.config.sslKeyPath == "" {
            return nil, errors.New("для SSL необходимы пути к сертификату и ключу")
        }
    }
    
    return &b.config, nil
}

func (sc *ServerConfig) String() string {
    return fmt.Sprintf("ServerConfig{Host: %s, Port: %d, SSL: %v, MaxConns: %d}",
        sc.host, sc.port, sc.enableSSL, sc.maxConns)
}

func main() {
    // Пример с User
    fmt.Println("=== СОЗДАНИЕ ПОЛЬЗОВАТЕЛЯ ===")
    user, err := NewUser(1, "test@example.com", "Тест Пользователь")
    if err != nil {
        fmt.Printf("Ошибка создания пользователя: %v\n", err)
        return
    }
    
    fmt.Println("Создан пользователь:", user)
    
    // Изменение email
    err = user.SetEmail("new@example.com")
    if err != nil {
        fmt.Printf("Ошибка изменения email: %v\n", err)
    } else {
        fmt.Println("Email изменён:", user.Email())
    }
    
    // Пример с Builder
    fmt.Println("\n=== КОНФИГУРАЦИЯ СЕРВЕРА ===")
    config, err := NewServerConfigBuilder().
        Host("0.0.0.0").
        Port(8443).
        Timeout(60*time.Second).
        MaxConnections(500).
        EnableSSL("/etc/ssl/cert.pem", "/etc/ssl/key.pem").
        LogLevel("debug").
        Build()
    
    if err != nil {
        fmt.Printf("Ошибка создания конфигурации: %v\n", err)
        return
    }
    
    fmt.Println("Создана конфигурация:", config)
}
```

##### 🔸 Setter и getter для типов
> через сеттеры/геттеры можно инкапсулировать типы и его методы
> 
```go
// к примеру создаем файл date.go

// тип экспортируем, но не поля
// их будем отдавать через get-методы
type Date struct {
	year int
	month int
}

// в получателе передаем указатель, чтоб обновляласьне копия, а исходное значение
func (d *Date) setYear(year int) error {
	if year < 1 {
		return errors.New("invalid year")
	}
	d.year = year
	return nil
}
func (d *Date) setMonth(month int) error {
	if month < 1 || month > 12 {
		return errors.New("invalid month")
	}
	d.Month = month
	return nil
}
// геттеры
func (d *Date) Year() int {
	return d.year
}
func (d *Date) Month() int {
	return d.month
}


func main() {
	date := Date{}
	// Автоматически преобразуется в указатель.
	err := date.SetYear(2019)
	if err != nil {
		log.Fatal(err)
	}
	err := date.SetMonth(2)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(date.Year())
	fmt.Println(date.Month())
}

```
---
