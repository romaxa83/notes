#python #pandas #package  #excel #csv #json

#### 🔹 Чтение и запись файлов JSON

```python
nobel = pd.read_json("nobel.json")
nobel.head()
# Out
# prizes
# ------------------------------------------------------------------------
# 0 {'year': '2019', 'category': 'chemistry', 'laureates': [{'id': '97...
# 1 {'year': '2019', 'category': 'economics', 'laureates': [{'id': '98...

# нормализуем данные
chemistry_2019 = nobel.loc[0, "prizes"]
pd.json_normalize(data = chemistry_2019)
# Out
# year category laureates
# ---------------------------------------------------------------------
# 0 2019 chemistry [{'id': '976', 'firstname': 'John', 'surname':...

pd.json_normalize(data = chemistry_2019, record_path = "laureates")
# Out
# id firstname surname motivation share
# ----------------------------------------------------------------
# 0 976 John Goodenough "for the development of li... 3
# 1 977 M. Stanley Whittingham "for the development of li... 3

pd.json_normalize(
	data = chemistry_2019,
	record_path = "laureates",
	meta = ["year", "category"],
)
# Out
# id firstname surname motivation share year category
# ---------------------------------------------------------------------------
# 0 976 John Goodenough "for the develop... 3 2019 chemistry
# 1 977 M. Stanley Whittingham "for the develop... 3 2019 chemistry

def add_laureates_key(entry):
	entry.setdefault("laureates", [])
nobel["prizes"].apply(add_laureates_key)
winners = pd.json_normalize(
	data = nobel["prizes"],
	record_path = "laureates",
	meta = ["year", "category"]
)
```

==запись в файл json==
```python
# Pandas запишет строку в файл JSON в том же каталоге
winners.to_json("winners.json", orient = "records")

#В параметре orient также можно передать аргументы "index", "split", "columns", "values" и "table".
```
---
#### 🔹 Чтение и запись файлов  CSV

```python
# если есть url который отдает файл - csv, то можно передавать его
url = "https://data.cityofnewyork.us/api/views/25th-nujf/rows.csv"
baby_names = pd.read_csv(url)

# запись в файл
baby_names.head(10).to_csv("NYC_Baby_Names.csv")
# Индекс можно исключить из вывода
baby_names.head(10).to_csv("NYC_Baby_Names.csv", index = False)
# также можно указать, какие столбцы экспортировать, передав список их имен в параметр columns
baby_names.to_csv(
	"NYC_Baby_Names.csv",
	index = False,
	columns = ["Gender", "Child's First Name", "Count"]
)
```
---
#### 🔹 Чтение и запись файлов  Excel

```python
pd.read_excel("Single Worksheet.xlsx")
# также можно передать ряд параметров
pd.read_excel(
	io = "Single Worksheet.xlsx",
	usecols = ["City", "First Name", "Last Name"],
	index_col = "City"
)
```

> По умолчанию pandas импортирует из excel только первую таблицу

```python
# Указывает какую таблицу импортировать
# код одинаковые
pd.read_excel("Multiple Worksheets.xlsx", sheet_name = 0)
pd.read_excel("Multiple Worksheets.xlsx", sheet_name = "Data 1")

# импортировать все таблицы, тогда вернеться словарь с именами листов в качестве ключей и соответствующими наборами данных DataFrame в качестве значений
workbook = pd.read_excel(
	"Multiple Worksheets.xlsx", sheet_name = None
)

# импорт нескольких таблиц
pd.read_excel(
	"Multiple Worksheets.xlsx",
	sheet_name = ["Data 1", "Data 3"]
)
```

Чтобы записать наборы данных в таблицу Excel - нужно создать объект ExcelWriter, который служит основой будущей книги, а затем прикрепить к нему отдельные листы
```python
girls = baby_names[baby_names["Gender"] == "FEMALE"]
boys = baby_names[baby_names["Gender"] == "MALE"]
excel_file = pd.ExcelWriter("Baby_Names.xlsx")

girls.to_excel(
	excel_writer = excel_file, sheet_name = "Girls", index = False
)
boys.to_excel(
	excel_file,
	sheet_name = "Boys",
	index = False,
	columns = ["Child's First Name", "Count", "Rank"]
)

excel_file.save()
```