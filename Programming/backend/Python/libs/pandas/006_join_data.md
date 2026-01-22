#python #pandas #package

Библиотека отлично справляется с добавлением, конкатенацией, соединением, слиянием и комбинированием наборов данных DataFrame как в вертикальном, так и в горизонтальном направлениях. Она может идентифицировать уникальные и общие записи между наборами DataFrame, выполнять операции SQL, такие как внутренние, внешние, левые и правые соединения.

==набор данных для примеров==
```python
groups1 = pd.read_csv("meetup/groups1.csv")
groups1.head()
# Out
# group_id     name           category_id  city_id
# ------------------------------------------------
# 0 6388 Alternative Health NYC       14    10001
# 1 6510 Alternative Energy Meetup    4     10001
# 2 8458 NYC Animal Rights            26    10001
# 3 8940 The New York City Anime      29    10001
# 4 10104 NYC Pit Bull Group          26    10001

groups2 = pd.read_csv("meetup/groups2.csv")
# Out
#   group_id   name                      category_id   city_id
# -------------------------------------------------------------
# 0 18879327 BachataMania                             5  10001
# 1 18880221 Photoshoot Chicago - Photography and ... 27 60601
# 2 18880426 Chicago Adult Push / Kick Scooter Gro... 31 60601
# 3 18880495 Chicago International Soccer Club        32 60601
# 4 18880695 Impact.tech San Francisco Meetup         2  94101

categories = pd.read_csv("meetup/categories.csv")
# Out
# category_id category_name
# ---------------------------------------
# 0 1      Arts & Culture
# 1 3      Cars & Motorcycles
# 2 4      Community & Environment
# 3 5      Dancing
# 4 6      Education & Learning

cities = pd.read_csv(
	"meetup/cities.csv", dtype = {"zip": "string"}
).head()
# Out
#   id    city         state    zip
# -------------------------------------
# 0 7093  West New York   NJ    07093
# 1 10001 New York        NY    10001
# 2 13417 New York Mills  NY    13417
# 3 46312 East Chicago    IN    46312
# 4 56567 New York Mills  MN    56567
```
---
#### 🔹 Конкатенация

Самый простой способ объединить два набора данных — конкатенация, добавление одного набора данных DataFrame в конец другого.

```python
# данные из groups2 будут добавлены в конец groups1
pd.concat(objs = [groups1, groups2])

# при объединение сохраняются индексы из обоих наборов данных, чтоб это сбросить используется ignore_index
pd.concat(objs = [groups1, groups2], ignore_index = True)

# если нужно создать неповторяющиеся индексы, но при этом сохранить информацию о том, из какого набора данных DataFrame получена каждая запись, можно использовать keys
pd.concat(objs = [groups1, groups2], keys = ["G1", "G2"])
# Out
#      group_id name category_id city_id
# --------------------------------------------------------
# G1 0 6388 Alternative Health NYC 14 10001
#    1 6510 Alternative Energy Meetup 4 10001
#    2 8458 NYC Animal Rights 26 10001
#    3 8940 The New York City Anime Group 29 10001
#    4 10104 NYC Pit Bull Group 26 10001
# ... ... ... ... ... ...
# G2 8326 26377464 Shinect 34 94101
#    8327 26377698 The art of getting what you wan... 14 94101
#    8328 26378067 Streeterville Running Group 9 60601
#    8329 26378128 Just Dance NYC 23 10001
#    8330 26378470 FREE Arabic Chicago Evanston No... 31 60601
```

##### 🔸 Отсутствие значений
При объединении двух DataFrame библиотека pandas подставит значение `NaN` на пересечении строк и столбцов, которые не являются общими для исходных наборов данных.

```python
sports_champions_A = pd.DataFrame(
	data = [
		["New England Patriots", "Houston Astros"],
		["Philadelphia Eagles", "Boston Red Sox"]
	],
	columns = ["Football", "Baseball"],
	index = [2017, 2018]
)
# Out
# Football Baseball
# ------------------------------------------
# 2017 New England Patriots Houston Astros
# 2018 Philadelphia Eagles Boston Red Sox

sports_champions_B = pd.DataFrame(
	data = [
		["New England Patriots", "St. Louis Blues"],
		["Kansas City Chiefs", "Tampa Bay Lightning"]
	],
	columns = ["Football", "Hockey"],
	index = [2019, 2020]
)
# Out
# Football Hockey
# -----------------------------------------------
# 2019 New England Patriots St. Louis Blues
# 2020 Kansas City Chiefs Tampa Bay Lightning

pd.concat(objs = [sports_champions_A, sports_champions_B])
# Out
#      Football                Baseball          Hockey
# ---------------------------------------------------------------
# 2017 New England Patriots    Houston Astros    NaN
# 2018 Philadelphia Eagles     Boston Red Sox    NaN
# 2019 New England Patriots    NaN               St. Louis Blues
# 2020 Kansas City Chiefs      NaN               Tampa Bay Lightning

# если в наборах разные данные но одинаковае индексы, то возможна такая ситуация
sports_champions_C = pd.DataFrame(
	data = [
		["Pittsburgh Penguins", "Golden State Warriors"],
		["Washington Capitals", "Golden State Warriors"]
	],
	columns = ["Hockey", "Basketball"],
	index = [2017, 2018]
)
pd.concat(objs = [sports_champions_A, sports_champions_C])
# Out
#     Football         Baseball        Hockey          Basketball
# --------------------------------------------------------------------------
# 2017 New England P... Houston Astros  NaN              NaN
# 2018 Philadelphia ... Boston Red Sox  NaN              NaN
# 2017 NaN              NaN             Pittsburgh Pe... Golden State ...
# 2018 NaN              NaN             Washington Ca... Golden State ...

# чтоб избежать такого результата - используем параметр axis
# Следующие два вызова функции concat эквивалентны
pd.concat(
	objs = [sports_champions_A, sports_champions_C],
	axis = 1
)
pd.concat(
	objs = [sports_champions_A, sports_champions_C],
	axis = "columns"
)
# Out
#     Football         Baseball        Hockey          Basketball
# --------------------------------------------------------------------------
# 2017 New England P... Houston Astros  Pittsburgh Pe... Golden State ...
# 2018 Philadelphia ... Boston Red Sox  Washington Ca... Golden State ...
```
---
#### 🔹 Left join
Левое соединение (`left join`) использует ключи из одного набора данных для извлечения значений из другого. Левое соединение подходит для случаев, когда один набор данных находится в центре внимания, а второй используется для предоставления дополнительной информации, связанной с первым набором.

```python
# для обьединения используется метод merge
groups.merge(categories, how = "left", on = "category_id").head()
# Out 
#  group_id name      category_id city_id    category_name
# ---------------------------------------------------------------------------
# 0 6388 Alternative Heal...  14    10001    Health & Wellbeing
# 1 6510 Alternative Ener...  4     10001    Community & Envi...
# 2 8458 NYC Animal Rights    26    10001    NaN
# 3 8940 The New York Cit...  29    10001    Sci-Fi & Fantasy
# 4 10104 NYC Pit Bull Group  26    10001    NaN

# если категория не нашлась то будет простален NaN
```
---
#### 🔹 Inner join
Внутреннее соединение (`inner join`) извлекает значения, существующие в двух наборах данных.Выполняя внутреннее соединение, pandas исключает значения, существующие только в каком-то одном наборе данных.

```python
groups.merge(categories, how = "inner", on = "category_id")
# Out
#  group_id name category_id city_id category_name
# -------------------------------------------------------
# 0 6388   Alternative He... 14 10001 Health & Wellb...
# 1 54126  Energy Healers... 14 10001 Health & Wellb...
# 2 67776  Flourishing Li... 14 10001 Health & Wellb...
# 3 111855 Hypnosis & NLP... 14 10001 Health & Wellb...
# 4 129277 The Live Food ... 14 60601 Health & Wellb...
```
---
#### 🔹 Outer join
Внешнее соединение (`outer join`) объединяет все записи из двух наборов данных. Исключительное вхождение значений в наборы не имеет никакого значения для внешнего соединения.

```python
groups.merge(
	cities, how = "outer", left_on = "city_id", right_on = "id"
)
# Out
# group_id name category_id city_id city state zip
# -------------------------------------------------------------------------
# 0 6388.0 Altern... 14.0 10001.0 New York NY 10001
# 1 6510.0 Altern... 4.0 10001.0 New York NY 10001
# 2 8458.0 NYC An... 26.0 10001.0 New York NY 10001
# 3 8940.0 The Ne... 29.0 10001.0 New York NY 10001
# 4 10104.0 NYC Pi... 26.0 10001.0 New York NY 10001

# В параметре indicator можно передать значение True методу merge, чтобы тот сообщил, какому DataFrame принадлежит каждое значение. Объединенный DataFrame будет включать столбец _merge со значениями "both", "left_only" и "right_only":
outer_join = groups.merge(
	cities,
	how = "outer",
	left_on = "city_id",
	right_on = "id",
	indicator = True
)
# Out
# group_id name category_id city_id city state zip _merge
# --------------------------------------------------------------
# 0 6388.0 Alt... 14.0 100... New... NY 10001 both
# 1 6510.0 Alt... 4.0 100... New... NY 10001 both

# Столбец _merge можно использовать для фильтрации строк, принадлежащих тому или иному DataFrame.
in_right_only = outer_join["_merge"] == "right_only"
outer_join[in_right_only].head()
```
---
#### 🔹 Слияние по индексным меткам 
Представьте, что DataFrame, участвующий в соединении, хранит первичные ключи в своем индексе.

```python
cities = cities.set_index("id")
# Out
# id city state zip
# ----------------------------------
# 7093 West New York NJ 07093
# 10001 New York NY 10001
# 13417 New York Mills NY 13417

groups.merge(
	cities,
	how = "left",
	left_on = "city_id",
	right_index = True # связь по индексу
)

# Метод merge имеет также дополнительный параметр left_index. В нем можно передать значение True, чтобы pandas искала совпадения в индексе левого DataFrame. Левый DataFrame — это набор данных, для которого вызывается метод merge.
```