#go #testing 

#### 🔹 Info

Go включает пакет `testing`, который может использоваться для написания авто-тестов, и 
команду `go test`, предназначенную для выполнения этих тестов при создании файлов с тестами, нужно называть файл - `some_test.go` (обязательно - `_test.go`), также имена в тестовых функциях должны называться на `Test`. Часто файлы с тестами находяться в том же пакете который и тестируется.

==запускать можно и конкретные пакеты==
```bash
go test github.com/romaxa83/study/fortest/prose 
```

> Можно добавить флаг `-v` , для получения более детальной информации по запуску тестов

Чтоб запустить конкретные тесты, нужно использовать `-run` и название функции после слова Test (запустятся все тесты которые будут начинаться на введеный текст, если не введена полное название теста)

==простой пример теста==
```go
// Функции передается указатель на значение testing.T.
func TestTwoElements(t *testing.T) {
	// Вызываем метод для testing.T тест не должен проходить.
	// переданое сообщение будет выведено в консоли
	t.Error("no test written yet")
}
```


> при компиляции файлы с тестами будут игнорироваться

```bash
# запустит тесты во вложеных папках
go test ./...

# покажет процент покрытие тестами
go test -cover ./...

# создаст файл(cover.out), где будет указано - что покрыто тестами ,а что нет
go test -coverprofile=cover.out ./...
# сгенерит html файл , для просмотра, из файла cover.out
go tool cover -html=cover.out -o cover.html
```
---
#### 🔹 Example

```go
// есть метод, который склеивает переданый срез слов
func JoinWithCommas(phrases []string) string {
	if len(phrases) == 1 {
		return phrases[0]
	} else if len(phrases) == 2 {
		return phrases[0] + " and " + phrases[1]
	} else {
		result := strings.Join(phrases[:len(phrases)-1], ", ")
		result += ", and "
		result += phrases[len(phrases)-1]

		return result
	}
}

// тестовые методы для него
func TestOneElement(t *testing.T) {
	list := []string{"apple"}
	// ожидаемое значение
	want := "apple"
	// полученое значение
	got := JoinWithCommas(list)

	if got != want{
		t.Error(errorString(list, got, want))
	}
}

// Функции передается указатель на значение testing.T.
func TestTwoElements(t *testing.T) {
	list := []string{"apple", "orange"}
	// ожидаемое значение
	want := "apple and orange"
	// полученое значение
	got := JoinWithCommas(list)

	if got != want{
		t.Error(errorString(list, got, want))
	}
}

func TestThreeElements(t *testing.T) {
	list := []string{"apple", "melon", "pear"}
	want := "apple, melon, and pear"
	got := JoinWithCommas(list)
	if got != want {
		t.Error(errorString(list, got, want))
	}
}

func errorString(list []string, got string, want string) string {
	return fmt.Sprintf("JoinWithCommas(%#v) = \"%s\", want \"%s\"", list, got, want)
}
```
---
#### 🔹 Табличные тесты

Если нужно протестировать один метод , на разные вариации, как в примере выше, то имеет смысл построить таблицу входных данных и ожидаемых результатов, а затем использовать одну тестовую функцию для проверки всех вариаций.

==пример на основе примере выше==
```go
// создаем стркуктуру
type testData struct {
	list []string
	want string
}

func TestJoinWithCommas(t *testing.T) {
	tests := []testData{
	// Создание сегмента значений testData.	
	testData{list: []string{"apple"}, want: "apple"},		
	testData{list: []string{"apple", "orange"}, want: "apple and orange"},	
	testData{list: []string{"apple", "orange", "pear"}, want: "apple, orange, and pear"},
	}
    // Обрабатывает каждое значение testData в сегменте.
	for _, test := range tests {
	got := JoinWithCommas(test.list)
		
	if got != test.want {
			t.Errorf("JoinWithCommas(%#v) = \"%s\", want \"%s\"", test.list, got, test.want)
		}
	}
}
```
---
#### 🔹 Тестирование БД

нужно использовать `mock`, библиотека для создании mock, sql баз данных - github.com/DATA-DOG/go-sqlmock

```go
// к примеру у нас есть репозиторий, и там есть
// приблизительно такой метод

func NewRepo(db *sql.DB) *Repo {
	return &Repo{db: db}
}

func (r *Repo) GetById(id int)(entity.Item, error) {
	item := entity.Item{}
	rows, err := r.db.Query("SELECT id, title FROM items WHERE id=?", id)
	if err != nil {
		return item, err
	}
	defer rows.Close()
	
	for rows.Next() {
		err = rows.Scan(&item.ID, &item.Title)
		if err != nil {
			return item, err
		}
	}
	
	return item, nil
}

// под него создается такой тестовый метод

func TestGetByIdSuccess(t *testing.T) {
	// здесь создаем конект с виртуальной бд
	db, mock, err := sqlmock.New()
	if err != nil {
		t.Fatal("mock fail", err)
	}
	defer db.Close()

	repo := NewRepo(db)

	// обьявляем какие колонки будут храниться в виртуальной бд
	rows := sqlmock.NewRows([]string{"id", "title"})
	// ожидаемая структура
	expect := entity.Item{
		ID:    10,
		Title: "test title",
	}
	// добавляем данные в виртуальные таблицу
	rows.AddRow(expect.ID, expect.Title)

	//в ExpectQuery - тот запрос который полетит а бд при вызове getById()
	mock.
		ExpectQuery("SELECT id, title FROM  items WHERE id = ?").
		WithArgs(expect.ID).
		WillReturRows(rows)

		// так мы можем проверить какие вернет ошибки, если тестовый метод
		// проверяет возврат ошибок
		// WillReturnErrors(err)

	item, err := repo.GetById(10)
	if err != nil {
		t.Errorf("fail to get data %+v", err)
		return
	}

	if err := mock.ExpectationsWereMet(); err != nil {
		t.Errorf("expectations were not mett %+v", err)
		return
	}

	if item.ID != expect.ID && item.Title != expect.Title {
		t.Errorf("result not match, want %v, get %v", expect, item)
		return
	}
}
```
---
