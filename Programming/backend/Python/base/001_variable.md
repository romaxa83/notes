#python #variable

#### 🔹 Variable

**Переменная** в Python - это имя, которое ссылается на объект в памяти. В отличие от многих других языков, в Python переменные не имеют типа - тип имеет объект, на который ссылается переменная.

```python
# Создание переменных
name = "Alice"
age = 25
height = 1.75
is_student = True

# Базовые аннотации типов
name: str = "Alice"
age: int = 25
height: float = 1.75
is_active: bool = True

print(type(name))      # <class 'str'>
print(type(age))       # <class 'int'>
print(type(height))    # <class 'float'>
print(type(is_student)) # <class 'bool'>
```

##### 🔸 Правила именования переменных
```python
# Корректные имена переменных
user_name = "John"
userName = "John"  # camelCase (не рекомендуется в Python)
user_name_2 = "John"
_private_var = "secret"
__dunder_var__ = "special"

# Некорректные имена переменных
# 2user = "John"      # SyntaxError: начинается с цифры
# user-name = "John"  # SyntaxError: содержит дефис
# class = "Python"    # SyntaxError: зарезервированное слово

# Рекомендации PEP 8
user_name = "John"      # snake_case - рекомендуется
CONSTANT_VALUE = 100    # UPPER_CASE для констант
_internal_var = "data"  # начинается с _ для внутренних переменных
```

##### 🔸 Множественное присваивание
```python
# Присваивание одного значения нескольким переменным
a = b = c = 0
print(a, b, c)  # 0 0 0

# Распаковка (unpacking)
x, y, z = 1, 2, 3
print(x, y, z)  # 1 2 3

# Обмен значениями
a, b = 10, 20
a, b = b, a
print(a, b)  # 20 10

# Распаковка списков
numbers = [1, 2, 3, 4, 5]
first, *middle, last = numbers
print(first)   # 1
print(middle)  # [2, 3, 4]
print(last)    # 5

# Игнорирование значений с помощью _
data = ("Alice", 25, "Engineer", "New York")
name, age, _, city = data
print(name, age, city)  # Alice 25 New York
```

##### 🔸 Расширенные операторы присваивания
```python
# Арифметические операторы присваивания
x = 10
x += 5   # x = x + 5
print(x) # 15

x -= 3   # x = x - 3
print(x) # 12

x *= 2   # x = x * 2
print(x) # 24

x //= 4  # x = x // 4 (целочисленное деление)
print(x) # 6

x %= 4   # x = x % 4 (остаток от деления)
print(x) # 2

x **= 3  # x = x ** 3 (возведение в степень)
print(x) # 8

# Операторы присваивания для строк
text = "Hello"
text += " World"
print(text)  # Hello World

# Операторы присваивания для списков
numbers = [1, 2, 3]
numbers += [4, 5]
print(numbers)  # [1, 2, 3, 4, 5]

numbers *= 2
print(numbers)  # [1, 2, 3, 4, 5, 1, 2, 3, 4, 5]
```

##### 🔸 Соглашения об именовании
```python
# Константы (модульный уровень)
MAX_CONNECTIONS = 100
DEFAULT_TIMEOUT = 30
API_BASE_URL = "https://api.example.com"

# Обычные переменные
user_name = "alice"
total_count = 0
is_valid = True

# Приватные переменные (начинаются с _)
_internal_cache = {}
_temp_data = []

# "Магические" переменные (окружены __)
__version__ = "1.0.0"
__author__ = "Your Name"
```
---
#### 🔹 Типы данных переменных
##### 🔸 Числовые
```python
# int - целые числа
integer_num = 42
negative_num = -17
big_num = 1234567890123456789012345678901234567890

# Различные системы счисления
binary_num = 0b1010      # 10 в двоичной
octal_num = 0o12         # 10 в восьмеричной  
hex_num = 0xA            # 10 в шестнадцатеричной

print(binary_num, octal_num, hex_num)  # 10 10 10

# float - числа с плавающей точкой
float_num = 3.14159
scientific_num = 1.5e10  # 15000000000.0
small_num = 1.5e-10      # 1.5e-10

# complex - комплексные числа
complex_num = 3 + 4j
print(complex_num.real)  # 3.0
print(complex_num.imag)  # 4.0
```

##### 🔸 Строковые типы
```python
# Различные способы создания строк
single_quote = 'Hello'
double_quote = "Hello"
triple_quote = """Многострочная
строка"""

# Экранирование символов
escaped_string = "He said \"Hello\" to me"
path_string = r"C:\Users\name\file.txt"  # raw string

# f-строки (форматированные строки)
name = "Alice"
age = 25
formatted = f"My name is {name} and I'm {age} years old"
print(formatted)  # My name is Alice and I'm 25 years old

# Форматирование с выражениями
price = 19.99
formatted_price = f"Price: ${price:.2f}"
print(formatted_price)  # Price: $19.99

# Старые способы форматирования
old_format = "Name: %s, Age: %d" % (name, age)
new_format = "Name: {}, Age: {}".format(name, age)
```

##### 🔸 Логический тип
```python
# Булевы значения
is_true = True
is_false = False

# Преобразование к bool
print(bool(1))        # True
print(bool(0))        # False
print(bool("hello"))  # True
print(bool(""))       # False
print(bool([1, 2]))   # True
print(bool([]))       # False
print(bool(None))     # False

# Использование в условиях
data = [1, 2, 3]
if data:  # Проверка на непустоту
    print("List is not empty")
```

##### 🔸 None тип
```python
# None - специальное значение "ничего"
empty_var = None
print(type(empty_var))  # <class 'NoneType'>

# Проверка на None
if empty_var is None:
    print("Variable is None")

# None в функциях (значение по умолчанию)
def greet(name=None):
    if name is None:
        return "Hello, Anonymous!"
    return f"Hello, {name}!"

print(greet())        # Hello, Anonymous!
print(greet("Alice")) # Hello, Alice!
```
---
#### 🔹 Области видимости переменных
##### 🔸 Локальные переменные
```python
def my_function():
    # Локальная переменная
    local_var = "I'm local"
    print(local_var)

my_function()  # I'm local
# print(local_var)  # NameError: name 'local_var' is not defined
```

##### 🔸 Глобальные переменные
```python
# Глобальная переменная
global_var = "I'm global"

def access_global():
    # Доступ к глобальной переменной
    print(global_var)

def modify_global():
    global global_var
    global_var = "Modified global"

access_global()   # I'm global
modify_global()
access_global()   # Modified global

# Создание новой глобальной переменной
def create_global():
    global new_global_var
    new_global_var = "New global"

create_global()
print(new_global_var)  # New global
```

##### 🔸 Нелокальные переменные (nonlocal)
```python
def outer_function():
    outer_var = "I'm in outer function"
    
    def inner_function():
        nonlocal outer_var
        outer_var = "Modified by inner function"
        print(f"Inner: {outer_var}")
    
    print(f"Before: {outer_var}")
    inner_function()
    print(f"After: {outer_var}")

outer_function()
# Before: I'm in outer function
# Inner: Modified by inner function
# After: Modified by inner function
```

##### 🔸 LEGB правило
Правило LEGB (Local, Enclosing, Global, Built-in) ==описывает последовательность, в которой Python ищет имена (переменные, функции), начиная с локальной области видимости и заканчивая встроенной==. **L** (Local) — это текущая функция, **E** (Enclosing) — вложенные функции, **G** (Global) — глобальная область программы, и **B** (Built-in) — встроенные имена Python. Поиск прекращается, как только имя найдено, иначе возникает ошибка `NameError`.
```python
# LEGB: Local -> Enclosing -> Global -> Built-in

# Built-in
print(len([1, 2, 3]))  # Встроенная функция len

# Global
global_name = "Global"

def enclosing_function():
    # Enclosing
    enclosing_name = "Enclosing"
    
    def local_function():
        # Local
        local_name = "Local"
        
        # Поиск переменной: Local -> Enclosing -> Global -> Built-in
        print(f"Local: {local_name}")
        print(f"Enclosing: {enclosing_name}")
        print(f"Global: {global_name}")
        print(f"Built-in: {len}")
    
    local_function()

enclosing_function()
```
---
#### 🔹 Списки (Lists)

Списки - это упорядоченные изменяемые коллекции элементов, которые могут содержать объекты любых типов.

##### 🔸 Создание и основные операции
```python
# Создание списков
empty_list = []
numbers = [1, 2, 3, 4, 5]
mixed_types = [1, "hello", 3.14, True, [1, 2, 3]]
from_range = list(range(5))  # [0, 1, 2, 3, 4]
from_string = list("Python")  # ['P', 'y', 't', 'h', 'o', 'n']

print(f"Пустой список: {empty_list}")
print(f"Числа: {numbers}")
print(f"Смешанные типы: {mixed_types}")
print(f"Из range: {from_range}")
print(f"Из строки: {from_string}")

# Создание списка с повторяющимися элементами
zeros = [0] * 5  # [0, 0, 0, 0, 0]
repeated = ["hello"] * 3  # ["hello", "hello", "hello"]
print(f"Нули: {zeros}")
print(f"Повторения: {repeated}")

# List comprehension (генератор списка)
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
upper_words = [word.upper() for word in ["python", "java", "javascript"]]

print(f"Квадраты: {squares}")
print(f"Четные: {evens}")
print(f"Заглавные: {upper_words}")
```

##### 🔸 Индексация и срезы
```python
fruits = ["яблоко", "банан", "апельсин", "груша", "киви"]

# Доступ по индексу
print(f"Первый элемент: {fruits[0]}")
print(f"Последний элемент: {fruits[-1]}")
print(f"Предпоследний: {fruits[-2]}")

# Срезы (slicing)
print(f"Первые три: {fruits[:3]}")
print(f"Последние два: {fruits[-2:]}")
print(f"Средние элементы: {fruits[1:4]}")
print(f"Каждый второй: {fruits[::2]}")
print(f"Обратный порядок: {fruits[::-1]}")

# Изменение элементов
fruits[0] = "красное яблоко"
print(f"После изменения: {fruits}")
# ['красное яблоко', 'банан', 'апельсин', 'груша', 'киви']

# Изменение среза
fruits[1:3] = ["желтый банан", "сочный апельсин"]
print(f"После изменения среза: {fruits}")
# ['яблоко', 'желтый банан', 'сочный апельсин', 'груша', 'киви']

# Проверка длины и принадлежности
print(f"Длина списка: {len(fruits)}")
print(f"'банан' в списке: {'банан' in fruits}")
print(f"'манго' в списке: {'манго' in fruits}")
```

##### 🔸 Методы списков
```python
# Методы добавления элементов
numbers = [1, 2, 3]

# append() - добавляет один элемент в конец
numbers.append(4)
print(f"После append(4): {numbers}")

# insert() - вставляет элемент по индексу
numbers.insert(0, 0)  # Вставить 0 в начало
print(f"После insert(0, 0): {numbers}")

# extend() - добавляет элементы из итерируемого объекта
numbers.extend([5, 6, 7])
print(f"После extend([5, 6, 7]): {numbers}")

# Альтернативы extend
numbers += [8, 9]  # То же, что extend
print(f"После += [8, 9]: {numbers}")

# Методы удаления
numbers_copy = numbers.copy()

# remove() - удаляет первое вхождение элемента
numbers_copy.remove(5)
print(f"После remove(5): {numbers_copy}")

# pop() - удаляет и возвращает элемент по индексу
removed = numbers_copy.pop()  # Удаляет последний
print(f"Удален последний элемент: {removed}")
print(f"Список после pop(): {numbers_copy}")

removed_first = numbers_copy.pop(0)  # Удаляет первый
print(f"Удален первый элемент: {removed_first}")
print(f"Список после pop(0): {numbers_copy}")

# clear() - очищает список
temp_list = [1, 2, 3]
temp_list.clear()
print(f"После clear(): {temp_list}")

# del - удаляет элементы по индексу или срезу
numbers_del = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
del numbers_del[0]      # Удалить первый элемент
del numbers_del[2:5]    # Удалить элементы со 2 по 4 индекс
print(f"После del операций: {numbers_del}")

# Методы поиска и подсчета
items = [1, 2, 3, 2, 4, 2, 5]

# index() - находит индекс первого вхождения
first_two_index = items.index(2)
print(f"Индекс первой двойки: {first_two_index}")

# index() с диапазоном поиска
second_two_index = items.index(2, first_two_index + 1)
print(f"Индекс второй двойки: {second_two_index}")

# count() - подсчитывает количество вхождений
twos_count = items.count(2)
print(f"Количество двоек: {twos_count}")

# Сортировка и реверс
unsorted = [3, 1, 4, 1, 5, 9, 2, 6, 5]

# sort() - сортирует список на месте
sorted_list = unsorted.copy()
sorted_list.sort()
print(f"Отсортированный: {sorted_list}")

# sort() с обратным порядком
reverse_sorted = unsorted.copy()
reverse_sorted.sort(reverse=True)
print(f"Обратная сортировка: {reverse_sorted}")

# sort() с ключом
words = ["python", "java", "c", "javascript", "go"]
words_by_length = words.copy()
words_by_length.sort(key=len)  # Сортировка по длине
print(f"По длине: {words_by_length}")

# reverse() - разворачивает список
original = [1, 2, 3, 4, 5]
reversed_list = original.copy()
reversed_list.reverse()
print(f"Развернутый: {reversed_list}")

# sorted() - возвращает новый отсортированный список
original_unsorted = [3, 1, 4, 1, 5, 9, 2, 6, 5]
new_sorted = sorted(original_unsorted)  # Оригинал не изменяется
print(f"Оригинал: {original_unsorted}")
print(f"Новый отсортированный: {new_sorted}")
```
---
#### 🔹 Кортежи (Tuples)

Кортежи - это упорядоченные неизменяемые коллекции элементов. Они похожи на списки, но не могут быть изменены после создания. Другими словами, вы не сможете заменить, удалить или присоединить новые элементы к существующему кортежу.

##### 🔸 Создание и основные операции
```python
# Создание кортежей
empty_tuple = ()
single_item = (42,)  # Запятая обязательна для одноэлементного кортежа
numbers = (1, 2, 3, 4, 5)
mixed = (1, "hello", 3.14, True)
without_parentheses = 1, 2, 3, 4, 5  # Скобки необязательны

print(f"Пустой кортеж: {empty_tuple}")
print(f"Один элемент: {single_item}")
print(f"Числа: {numbers}")
print(f"Смешанные типы: {mixed}")
print(f"Без скобок: {without_parentheses}")

# Создание кортежа из других итерируемых объектов
from_list = tuple([1, 2, 3, 4])
from_string = tuple("Python")
from_range = tuple(range(5))

print(f"Из списка: {from_list}")
print(f"Из строки: {from_string}")
print(f"Из range: {from_range}")

# Кортежи неизменяемы
coordinates = (10, 20)
print(f"Координаты: {coordinates}")

# Это вызовет ошибку:
# coordinates[0] = 15  # TypeError: 'tuple' object does not support item assignment

# Но можно создать новый кортеж
new_coordinates = (15, coordinates[1])
print(f"Новые координаты: {new_coordinates}")
```

##### 🔸 Индексация и операции
```python
# Индексация и срезы (работают так же, как у списков)
fruits = ("яблоко", "банан", "апельсин", "груша", "киви")

print(f"Первый фрукт: {fruits[0]}")
print(f"Последний фрукт: {fruits[-1]}")
print(f"Первые три: {fruits[:3]}")
print(f"Каждый второй: {fruits[::2]}")

# Длина и принадлежность
print(f"Длина кортежа: {len(fruits)}")
print(f"'банан' в кортеже: {'банан' in fruits}")

# Методы кортежа
numbers = (1, 2, 3, 2, 4, 2, 5)

# count() - подсчет вхождений
count_of_twos = numbers.count(2)
print(f"Количество двоек: {count_of_twos}")

# index() - поиск индекса
index_of_four = numbers.index(4)
print(f"Индекс четверки: {index_of_four}")

# Конкатенация и повторение
tuple1 = (1, 2, 3)
tuple2 = (4, 5, 6)
combined = tuple1 + tuple2
repeated = tuple1 * 3

print(f"Объединенный: {combined}")
print(f"Повторенный: {repeated}")

# Сравнение кортежей
point1 = (1, 2)
point2 = (1, 3)
point3 = (2, 1)

print(f"(1, 2) < (1, 3): {point1 < point2}")  # True
print(f"(1, 2) < (2, 1): {point1 < point3}")  # True
print(f"(1, 3) < (2, 1): {point2 < point3}")  # True
```

##### 🔸 Распаковка кортежей
```python
# Базовая распаковка
point = (10, 20)
x, y = point
print(f"x = {x}, y = {y}")

# Распаковка с несколькими значениями
person_data = ("Alice", 28, "Engineer", "alice@example.com")
name, age, profession, email = person_data
print(f"Имя: {name}, Возраст: {age}, Профессия: {profession}")

# Распаковка с оператором *
numbers = (1, 2, 3, 4, 5, 6)
first, *middle, last = numbers
print(f"Первый: {first}")
print(f"Средние: {middle}")
print(f"Последний: {last}")

# Распаковка в функциях
def calculate_distance(point1, point2):
    """Вычисляет расстояние между двумя точками"""
    x1, y1 = point1
    x2, y2 = point2
    return ((x2 - x1)**2 + (y2 - y1)**2)**0.5

point_a = (0, 0)
point_b = (3, 4)
distance = calculate_distance(point_a, point_b)
print(f"Расстояние между {point_a} и {point_b}: {distance}")

# Обмен значений
a, b = 10, 20
print(f"До обмена: a = {a}, b = {b}")
a, b = b, a  # Обмен через кортеж
print(f"После обмена: a = {a}, b = {b}")

# Множественное присваивание
def get_name_age():
    return "Bob", 25

name, age = get_name_age()
print(f"Получены данные: {name}, {age} лет")

# Игнорирование значений при распаковке
data = ("Charlie", 30, "Manager", "New York", "USA")
name, age, _, city, _ = data  # Профессия и страна игнорируются
print(f"{name}, {age} лет, живет в {city}")

# Вложенная распаковка
nested_data = (("Alice", "Bob"), (25, 30))
(name1, name2), (age1, age2) = nested_data
print(f"Пара 1: {name1} ({age1} лет)")
print(f"Пара 2: {name2} ({age2} лет)")
```

##### 🔸 Именованные кортежи (Named Tuples)
```python
from collections import namedtuple
from typing import NamedTuple

# Создание именованного кортежа с collections.namedtuple
Point = namedtuple('Point', ['x', 'y'])
Person = namedtuple('Person', ['name', 'age', 'profession'])

# Создание экземпляров
origin = Point(0, 0)
destination = Point(10, 20)

alice = Person("Alice", 28, "Developer")
bob = Person("Bob", 35, "Manager")

print(f"Начальная точка: {origin}")
print(f"Конечная точка: {destination}")
print(f"Сотрудник: {alice}")

# Доступ к элементам по имени и индексу
print(f"Координата X: {origin.x}")
print(f"Координата Y: {origin[1]}")  # Тот же результат
print(f"Имя сотрудника: {alice.name}")
print(f"Возраст: {alice.age}")

# Именованные кортежи неизменяемы, но можно создавать новые
moved_point = origin._replace(x=5)
print(f"Перемещенная точка: {moved_point}")

older_alice = alice._replace(age=29)
print(f"Alice через год: {older_alice}")

# Преобразование в словарь
alice_dict = alice._asdict()
print(f"Alice как словарь: {alice_dict}")

# Современный подход с typing.NamedTuple
class Employee(NamedTuple):
    name: str
    age: int
    profession: str
    salary: float
    
    def annual_salary(self) -> float:
        """Вычисляет годовую зарплату"""
        return self.salary * 12
    
    def is_senior(self) -> bool:
        """Проверяет, является ли сотрудник старшим"""
        return self.age >= 30

# Создание и использование типизированного именованного кортежа
charlie = Employee("Charlie", 32, "Senior Developer", 8500.0)
print(f"Сотрудник: {charlie}")
print(f"Годовая зарплата: {charlie.annual_salary():.2f}")
print(f"Старший сотрудник: {charlie.is_senior()}")

# Использование именованных кортежей в функциях
def process_coordinates(points):
    """Обрабатывает список точек"""
    if not points:
        return Point(0, 0), 0
    
    total_x = sum(p.x for p in points)
    total_y = sum(p.y for p in points)
    center = Point(total_x / len(points), total_y / len(points))
    
    max_distance = max(
        ((p.x - center.x)**2 + (p.y - center.y)**2)**0.5 
        for p in points
    )
    
    return center, max_distance

# Тестирование функции
test_points = [Point(0, 0), Point(10, 0), Point(5, 5), Point(0, 10)]
center_point, max_dist = process_coordinates(test_points)
print(f"Центр масс: {center_point}")
print(f"Максимальное расстояние от центра: {max_dist:.2f}")

# Создание конфигурационных объектов
Config = namedtuple('Config', [
    'database_url',
    'api_key', 
    'debug_mode',
    'timeout'
])

app_config = Config(
    database_url="postgresql://localhost:5432/mydb",
    api_key="secret-key-123",
    debug_mode=True,
    timeout=30
)

print(f"Конфигурация приложения: {app_config}")
print(f"Режим отладки: {app_config.debug_mode}")
```

#### 🔹 Множества (Sets)

Множество — это неупорядоченный набор уникальных объектов. Оно используется для поиска неповторяющихся значений или решения проблем, связанных с принадлежностью. Элементами множеств обычно могут быть только неизменяемые объекты. Можно создать множество чисел, строк или кортежей, но не множество списков. В отличие от списков и кортежей, элементы множеств не упорядочены и не могут индексироваться числами. Еще во множестве не может быть повторяющихся элементов.

##### 🔸 Создание и основные операции
```python
# Создание множеств
empty_set = set()  # Пустое множество (не {}, это словарь!)
numbers = {1, 2, 3, 4, 5}
mixed = {1, "hello", 3.14, True}
from_list = set([1, 2, 2, 3, 3, 4])  # Дубликаты удаляются
from_string = set("hello")  # {'h', 'e', 'l', 'o'}

print(f"Пустое множество: {empty_set}")
print(f"Числа: {numbers}")
print(f"Смешанные типы: {mixed}")
print(f"Из списка с дубликатами: {from_list}")
print(f"Из строки: {from_string}")

# Множества содержат только уникальные элементы
duplicates = {1, 1, 2, 2, 3, 3}
print(f"Множество с дубликатами: {duplicates}")  # {1, 2, 3}

# Проверка принадлежности (очень быстрая операция)
print(f"2 в множестве numbers: {2 in numbers}")
print(f"10 в множестве numbers: {10 in numbers}")

# Длина множества
print(f"Размер множества numbers: {len(numbers)}")

# Множества изменяемы
mutable_set = {1, 2, 3}
print(f"Исходное множество: {mutable_set}")
```

##### 🔸 Методы множеств
```python
# Добавление элементов
fruits = {"яблоко", "банан"}
print(f"Исходные фрукты: {fruits}")

# add() - добавляет один элемент
fruits.add("апельсин")
print(f"После add('апельсин'): {fruits}")

# update() - добавляет элементы из итерируемого объекта
fruits.update(["груша", "киви"])
print(f"После update(['груша', 'киви']): {fruits}")

# update() может принимать несколько итерируемых объектов
fruits.update({"манго"}, ["ананас"])
print(f"После множественного update: {fruits}")

# Удаление элементов
test_set = {1, 2, 3, 4, 5}

# remove() - удаляет элемент, вызывает ошибку если элемента нет
test_set.remove(3)
print(f"После remove(3): {test_set}")

# discard() - удаляет элемент, не вызывает ошибку если элемента нет
test_set.discard(10)  # Элемента нет, но ошибки не будет
test_set.discard(4)   # Элемент есть, удалится
print(f"После discard(10) и discard(4): {test_set}")

# pop() - удаляет и возвращает произвольный элемент
removed = test_set.pop()
print(f"Удален элемент: {removed}")
print(f"После pop(): {test_set}")

# clear() - очищает множество
temp_set = {1, 2, 3}
temp_set.clear()
print(f"После clear(): {temp_set}")

# copy() - создает поверхностную копию
original = {1, 2, 3}
copied = original.copy()
copied.add(4)
print(f"Оригинал: {original}")
print(f"Копия: {copied}")
```

##### 🔸 Операции над множествами
```python
# Математические операции над множествами
set_a = {1, 2, 3, 4, 5}
set_b = {4, 5, 6, 7, 8}
set_c = {1, 2, 3}

print(f"Множество A: {set_a}")
print(f"Множество B: {set_b}")
print(f"Множество C: {set_c}")

# Объединение (union)
union_ab = set_a | set_b
union_method = set_a.union(set_b)
print(f"A ∪ B (|): {union_ab}") # A ∪ B (|): {1, 2, 3, 4, 5, 6, 7, 8}
print(f"A ∪ B (метод): {union_method}")
# A ∪ B (метод): {1, 2, 3, 4, 5, 6, 7, 8}

# Пересечение (intersection)
intersection_ab = set_a & set_b
intersection_method = set_a.intersection(set_b)
print(f"A ∩ B (&): {intersection_ab}")
print(f"A ∩ B (метод): {intersection_method}")

# Разность (difference)
difference_ab = set_a - set_b
difference_method = set_a.difference(set_b)
print(f"A - B (-): {difference_ab}")
print(f"A - B (метод): {difference_method}")

difference_ba = set_b - set_a
print(f"B - A: {difference_ba}")

# Симметричная разность (symmetric difference)
sym_diff_ab = set_a ^ set_b
sym_diff_method = set_a.symmetric_difference(set_b)
print(f"A △ B (^): {sym_diff_ab}")
print(f"A △ B (метод): {sym_diff_method}")

# Проверки отношений между множествами
print(f"C подмножество A: {set_c.issubset(set_a)}")  # True
print(f"A надмножество C: {set_a.issuperset(set_c)}")  # True
print(f"A и B не пересекаются: {set_a.isdisjoint(set_b)}")  # False

empty_set = set()
print(f"Пустое множество подмножество A: {empty_set.issubset(set_a)}")  # True

# Модифицирующие операции (изменяют исходное множество)
modify_set = {1, 2, 3}
print(f"Исходное множество: {modify_set}")

# |= (update)
modify_set |= {4, 5}
print(f"После |= {{4, 5}}: {modify_set}")

# &= (intersection_update)
modify_set &= {1, 2, 3, 4}
print(f"После &= {{1, 2, 3, 4}}: {modify_set}")

# -= (difference_update)
modify_set -= {2, 3}
print(f"После -= {{2, 3}}: {modify_set}")

# ^= (symmetric_difference_update)
modify_set ^= {1, 5, 6}
print(f"После ^= {{1, 5, 6}}: {modify_set}")
```

##### 🔸 Практические применения множеств
```python
# Удаление дубликатов из списка
numbers_with_duplicates = [1, 2, 3, 2, 4, 1, 5, 3, 6, 4]
unique_numbers = list(set(numbers_with_duplicates))
print(f"С дубликатами: {numbers_with_duplicates}")
print(f"Без дубликатов: {unique_numbers}")

# Поиск уникальных элементов в нескольких списках
list1 = [1, 2, 3, 4, 5]
list2 = [4, 5, 6, 7, 8]
list3 = [7, 8, 9, 10, 11]

# Элементы, которые есть во всех списках
common_elements = set(list1) & set(list2) & set(list3)
print(f"Общие элементы: {common_elements}")

# Элементы, которые есть только в первом списке
only_in_first = set(list1) - set(list2) - set(list3)
print(f"Только в первом списке: {only_in_first}")

# Все уникальные элементы из всех списков
all_unique = set(list1) | set(list2) | set(list3)
print(f"Все уникальные: {all_unique}")

# Анализ текста
def analyze_text(text1, text2):
    """Анализирует различия между двумя текстами по словам"""
    words1 = set(text1.lower().split())
    words2 = set(text2.lower().split())
    
    return {
        "common_words": words1 & words2,
        "unique_to_first": words1 - words2,
        "unique_to_second": words2 - words1,
        "all_words": words1 | words2
    }

text_a = "Python is a great programming language"
text_b = "Java is a powerful programming language"

analysis = analyze_text(text_a, text_b)
print("\nАнализ текстов:")
for key, value in analysis.items():
    print(f"{key}: {value}")

# Фильтрация пользователей
active_users = {"alice", "bob", "charlie", "diana"}
premium_users = {"bob", "diana", "eve", "frank"}
banned_users = {"charlie", "eve"}

# Активные премиум пользователи
active_premium = active_users & premium_users
print(f"Активные премиум пользователи: {active_premium}")

# Активные пользователи, которые не заблокированы
safe_active_users = active_users - banned_users
print(f"Безопасные активные пользователи: {safe_active_users}")

# Все пользователи, которые не заблокированы
all_safe_users = (active_users | premium_users) - banned_users
print(f"Все безопасные пользователи: {all_safe_users}")

# Проверка прав доступа
def has_access(user, required_permissions, user_permissions):
    """Проверяет, есть ли у пользователя необходимые права"""
    user_perms = set(user_permissions.get(user, []))
    required_perms = set(required_permissions)
    
    return required_perms.issubset(user_perms)

permissions_db = {
    "alice": ["read", "write", "execute"],
    "bob": ["read", "write"],
    "charlie": ["read"],
    "admin": ["read", "write", "execute", "delete", "admin"]
}

# Проверки доступа
print(f"Alice может писать: {has_access('alice', ['write'], permissions_db)}")
print(f"Bob может выполнять: {has_access('bob', ['execute'], permissions_db)}")
print(f"Charlie может писать и читать: {has_access('charlie', ['read', 'write'], permissions_db)}")
```

##### 🔸 Неизменяемые множества (frozenset)
```python
# Создание неизменяемых множеств
frozen_numbers = frozenset([1, 2, 3, 4, 5])
frozen_from_set = frozenset({1, 2, 3})
empty_frozen = frozenset()

print(f"Неизменяемое множество: {frozen_numbers}")
print(f"Из обычного множества: {frozen_from_set}")
print(f"Пустое неизменяемое: {empty_frozen}")

# frozenset можно использовать как ключ словаря или элемент множества
regular_set = {1, 2, 3}
# regular_set_as_key = {regular_set: "value"}  # Ошибка! set не хешируемый

frozen_set_as_key = {frozen_numbers: "frozen set as key"}
print(f"Словарь с frozenset как ключом: {frozen_set_as_key}")

# Множество из неизменяемых множеств
set_of_frozensets = {
    frozenset([1, 2, 3]),
    frozenset([4, 5, 6]),
    frozenset([1, 2, 3])  # Дубликат будет удален
}
print(f"Множество неизменяемых множеств: {set_of_frozensets}")

# frozenset поддерживает те же операции, что и set, но не изменяющие методы
frozen_a = frozenset([1, 2, 3, 4])
frozen_b = frozenset([3, 4, 5, 6])

print(f"Объединение: {frozen_a | frozen_b}")
print(f"Пересечение: {frozen_a & frozen_b}")
print(f"Разность: {frozen_a - frozen_b}")

# frozenset нельзя изменить
# frozen_numbers.add(6)  # Ошибка! AttributeError

# Практическое применение: кэширование результатов для множеств
cache = {}

def expensive_set_operation(items):
    """Дорогая операция над множеством с кэшированием"""
    frozen_items = frozenset(items)
    
    if frozen_items in cache:
        print("Результат взят из кэша")
        return cache[frozen_items]
    
    # Имитация дорогой операции
    result = sum(x**2 for x in frozen_items)
    cache[frozen_items] = result
    print("Результат вычислен и сохранен в кэш")
    
    return result

# Тестирование кэширования
print(f"Первый вызов: {expensive_set_operation([1, 2, 3])}")
print(f"Второй вызов: {expensive_set_operation([1, 2, 3])}")  # Из кэша
print(f"Другие данные: {expensive_set_operation([2, 3, 4])}")
print(f"Снова первые данные: {expensive_set_operation([1, 2, 3])}")  # Из кэша
```
---
#### 🔹 Словари (Dictionaries)

Словарь (dictionary) определяет соответствие между ключами и значениями. Для создания словаря заключите пары «ключ — значение», разделенные двоеточием, в фигурные скобки ({}). Словарь — удобный способ определить объект, состоящий из именованных полей. Но он часто используется и как определение отображений для быстрого поиска по неупорядоченным данным.

##### 🔸 Создание и основные операции
```python
# Создание словарей
empty_dict = {}
empty_dict_constructor = dict()

# Различные способы создания
person = {
    "name": "Alice", 
    "age": 25, 
    "profession": "Developer"
}

# Из списка кортежей
pairs = [("a", 1), ("b", 2), ("c", 3)]
dict_from_pairs = dict(pairs)

# Из ключевых слов
dict_from_kwargs = dict(name="Bob", age=30, city="Moscow")

# Из двух списков
keys = ["x", "y", "z"]
values = [10, 20, 30]
dict_from_zip = dict(zip(keys, values))

# Словарное включение (dictionary comprehension)
squares = {x: x**2 for x in range(1, 6)}
even_squares = {x: x**2 for x in range(1, 11) if x % 2 == 0}

print(f"Пустые словари: {empty_dict}, {empty_dict_constructor}")
print(f"Персона: {person}")
print(f"Из кортежей: {dict_from_pairs}")
print(f"Из kwargs: {dict_from_kwargs}")
print(f"Из zip: {dict_from_zip}")
print(f"Квадраты: {squares}")
print(f"Квадраты четных: {even_squares}")

# Доступ к элементам
print(f"Имя: {person['name']}")
print(f"Возраст: {person.get('age')}")
print(f"Зарплата: {person.get('salary', 'не указана')}")  # Значение по умолчанию

# Проверка наличия ключей
print(f"'name' в словаре: {'name' in person}")
print(f"'salary' в словаре: {'salary' in person}")

# Изменение и добавление элементов
person["age"] = 26  # Изменение существующего ключа
person["salary"] = 75000  # Добавление нового ключа
print(f"После изменений: {person}")

# Длина словаря
print(f"Количество ключей: {len(person)}")
```

##### 🔸 Методы словарей
```python
# Базовые методы
student = {
    "name": "Charlie",
    "age": 20,
    "grades": [4, 5, 3, 4, 5],
    "university": "MSU"
}

print(f"Исходный словарь: {student}")

# keys() - возвращает представление ключей
keys_view = student.keys()
print(f"Ключи: {list(keys_view)}")

# values() - возвращает представление значений  
values_view = student.values()
print(f"Значения: {list(values_view)}")

# items() - возвращает представление пар ключ-значение
items_view = student.items()
print(f"Элементы: {list(items_view)}")

# get() - безопасное получение значения
name = student.get("name")
country = student.get("country", "Russia")  # Значение по умолчанию
print(f"Имя: {name}")
print(f"Страна: {country}")

# setdefault() - возвращает значение или устанавливает по умолчанию
hobbies = student.setdefault("hobbies", [])
hobbies.append("reading")
print(f"После setdefault: {student}")

# update() - обновляет словарь
additional_info = {"gpa": 4.2, "year": 3}
student.update(additional_info)
print(f"После update: {student}")

# Можно обновлять через ключевые аргументы
student.update(scholarship=True, dormitory="Building A")
print(f"После update с kwargs: {student}")

# pop() - удаляет и возвращает значение по ключу
removed_age = student.pop("age")
removed_unknown = student.pop("unknown_key", "not found")
print(f"Удален возраст: {removed_age}")
print(f"Удаление несуществующего ключа: {removed_unknown}")
print(f"После pop: {student}")

# popitem() - удаляет и возвращает последнюю пару
last_item = student.popitem()
print(f"Последний элемент: {last_item}")
print(f"После popitem: {student}")

# clear() - очищает словарь
temp_dict = {"a": 1, "b": 2}
temp_dict.clear()
print(f"После clear: {temp_dict}")

# copy() - создает поверхностную копию
original = {"data": [1, 2, 3], "count": 10}
shallow_copy = original.copy()
shallow_copy["count"] = 20
shallow_copy["data"].append(4)  # Изменяет оригинал!

print(f"Оригинал: {original}")
print(f"Копия: {shallow_copy}")

# Глубокое копирование
import copy
deep_copy = copy.deepcopy(original)
deep_copy["data"].append(5)  # Не влияет на оригинал

print(f"После глубокого копирования:")
print(f"Оригинал: {original}")
print(f"Глубокая копия: {deep_copy}")
```

##### 🔸 Продвинутые операции со словарями
```python
# Объединение словарей (Python 3.9+)
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
dict3 = {"a": 10, "e": 5}  # "a" перезапишется

# Оператор | (объединение)
merged = dict1 | dict2
print(f"dict1 | dict2: {merged}")

# При конфликте ключей выигрывает правый операнд
conflict_merged = dict1 | dict3
print(f"dict1 | dict3: {conflict_merged}")

# Оператор |= (обновление на месте)
dict1_copy = dict1.copy()
dict1_copy |= dict2
print(f"После |=: {dict1_copy}")

# Старый способ объединения (работает во всех версиях)
merged_old = {**dict1, **dict2, **dict3}
print(f"Объединение через **: {merged_old}")

# Вложенные словари
company = {
    "name": "TechCorp",
    "employees": {
        "developers": ["Alice", "Bob", "Charlie"],
        "managers": ["Diana", "Eve"],
        "designers": ["Frank"]
    },
    "projects": {
        "web_app": {
            "status": "active",
            "team": ["Alice", "Diana", "Frank"],
            "deadline": "2024-06-01"
        },
        "mobile_app": {
            "status": "planning", 
            "team": ["Bob", "Eve"],
            "deadline": "2024-08-15"
        }
    }
}

print("Структура компании:")
print(f"Название: {company['name']}")
print(f"Разработчики: {company['employees']['developers']}")
print(f"Статус веб-приложения: {company['projects']['web_app']['status']}")

# Безопасный доступ к вложенным элементам
def safe_get(dictionary, *keys, default=None):
    """Безопасное получение значения по цепочке ключей"""
    for key in keys:
        if isinstance(dictionary, dict) and key in dictionary:
            dictionary = dictionary[key]
        else:
            return default
    return dictionary

# Примеры безопасного доступа
web_team = safe_get(company, "projects", "web_app", "team", default=[])
nonexistent = safe_get(company, "projects", "desktop_app", "status", default="not found")

print(f"Команда веб-приложения: {web_team}")
print(f"Несуществующий проект: {nonexistent}")

# Инвертирование словаря
original_dict = {"a": 1, "b": 2, "c": 3, "d": 2}  # "d" и "b" имеют одинаковое значение

# Простое инвертирование (может потерять данные при дублировании значений)
inverted_simple = {v: k for k, v in original_dict.items()}
print(f"Простое инвертирование: {inverted_simple}")

# Инвертирование с группировкой
from collections import defaultdict

inverted_grouped = defaultdict(list)
for key, value in original_dict.items():
    inverted_grouped[value].append(key)

inverted_grouped = dict(inverted_grouped)
print(f"Инвертирование с группировкой: {inverted_grouped}")

# Фильтрация словарей
scores = {
    "Alice": 95,
    "Bob": 87, 
    "Charlie": 92,
    "Diana": 78,
    "Eve": 91
}

# Высокие баллы (>= 90)
high_scores = {name: score for name, score in scores.items() if score >= 90}
print(f"Высокие баллы: {high_scores}")

# Сортировка словаря по значениям
sorted_by_score = dict(sorted(scores.items(), key=lambda x: x[1], reverse=True))
print(f"Сортировка по баллам: {sorted_by_score}")

# Группировка по категориям
def categorize_score(score):
    if score >= 90:
        return "excellent"
    elif score >= 80:
        return "good"
    else:
        return "needs_improvement"

categorized = {}
for name, score in scores.items():
    category = categorize_score(score)
    if category not in categorized:
        categorized[category] = []
    categorized[category].append((name, score))

print("Категоризация баллов:")
for category, students in categorized.items():
    print(f"  {category}: {students}")

# Подсчет элементов
from collections import Counter

text = "hello world hello python world"
words = text.split()

# Ручной подсчет
word_count = {}
for word in words:
    word_count[word] = word_count.get(word, 0) + 1

print(f"Ручной подсчет слов: {word_count}")

# С использованием Counter
counter_result = Counter(words)
print(f"Counter результат: {counter_result}")
print(f"Самые частые 2 слова: {counter_result.most_common(2)}")
```
---
#### 🔹 Производительность и выбор подходящей коллекции
```python
import time
import random
from collections import deque

def benchmark_collections():
    """Сравнение производительности различных коллекций"""
    
    # Размеры для тестирования
    sizes = [1000, 10000, 100000]
    
    print("=== Сравнение производительности коллекций ===\n")
    
    for size in sizes:
        print(f"Размер коллекции: {size:,}")
        
        # Подготовка данных
        data = list(range(size))
        random.shuffle(data)
        
        # Список
        list_time = time.time()
        test_list = []
        for item in data:
            test_list.append(item)
        list_append_time = time.time() - list_time
        
        # Поиск в списке
        list_time = time.time()
        found = 0
        search_items = random.sample(data, min(100, size))
        for item in search_items:
            if item in test_list:
                found += 1
        list_search_time = time.time() - list_time
        
        # Множество
        set_time = time.time()
        test_set = set()
        for item in data:
            test_set.add(item)
        set_add_time = time.time() - set_time
        
        # Поиск в множестве
        set_time = time.time()
        found = 0
        for item in search_items:
            if item in test_set:
                found += 1
        set_search_time = time.time() - set_time
        
        # Словарь
        dict_time = time.time()
        test_dict = {}
        for i, item in enumerate(data):
            test_dict[item] = i
        dict_assign_time = time.time() - dict_time
        
        # Поиск в словаре
        dict_time = time.time()
        found = 0
        for item in search_items:
            if item in test_dict:
                found += 1
        dict_search_time = time.time() - dict_time
        
        # Результаты
        print(f"  Добавление элементов:")
        print(f"    Список (append): {list_append_time:.4f}с")
        print(f"    Множество (add): {set_add_time:.4f}с")
        print(f"    Словарь (assign): {dict_assign_time:.4f}с")
        
        print(f"  Поиск {len(search_items)} элементов:")
        print(f"    Список (in): {list_search_time:.4f}с")
        print(f"    Множество (in): {set_search_time:.4f}с") 
        print(f"    Словарь (in): {dict_search_time:.4f}с")
        
        print(f"  Соотношение времени поиска:")
        if set_search_time > 0:
            print(f"    Список медленнее множества в {list_search_time/set_search_time:.1f} раз")
        
        print()

# Рекомендации по выбору коллекций
def collection_recommendations():
    """Рекомендации по выбору типа коллекции"""
    
    recommendations = {
        "Список (list)": {
            "Используйте когда": [
                "Нужен упорядоченный набор элементов",
                "Требуется доступ по индексу", 
                "Нужно добавлять элементы в конец",
                "Допустимы дубликаты",
                "Нужно сохранить порядок добавления"
            ],
            "Не используйте когда": [
                "Часто ищете элементы (медленный поиск O(n))",
                "Часто удаляете элементы из середины",
                "Нужны только уникальные элементы"
            ],
            "Сложность операций": {
                "Доступ по индексу": "O(1)",
                "Поиск элемента": "O(n)",
                "Вставка в конец": "O(1)",
                "Вставка в начало/середину": "O(n)",
                "Удаление из конца": "O(1)",
                "Удаление из начала/середины": "O(n)"
            }
        },
        
        "Кортеж (tuple)": {
            "Используйте когда": [
                "Нужен неизменяемый упорядоченный набор",
                "Используется как ключ словаря",
                "Возвращаете несколько значений из функции",
                "Нужна гарантия неизменности данных"
            ],
            "Не используйте когда": [
                "Нужно изменять элементы после создания",
                "Часто добавляете/удаляете элементы"
            ],
            "Преимущества": [
                "Неизменяемый (хешируемый)",
                "Чуть быстрее списка для чтения",
                "Меньше памяти чем список"
            ]
        },
        
        "Множество (set)": {
            "Используйте когда": [
                "Нужны только уникальные элементы",
                "Часто проверяете принадлежность элемента",
                "Выполняете операции пересечения/объединения",
                "Удаляете дубликаты"
            ],
            "Не используйте когда": [
                "Нужен доступ по индексу",
                "Важен порядок элементов",
                "Нужно хранить изменяемые объекты"
            ],
            "Сложность операций": {
                "Поиск элемента": "O(1) в среднем",
                "Добавление": "O(1) в среднем", 
                "Удаление": "O(1) в среднем",
                "Пересечение/объединение": "O(min(len(s1), len(s2)))"
            }
        },
        
        "Словарь (dict)": {
            "Используйте когда": [
                "Нужно связать ключи со значениями",
                "Требуется быстрый поиск по ключу",
                "Нужно подсчитать элементы",
                "Создаете индекс или кэш"
            ],
            "Не используйте когда": [
                "Нужен только набор ключей (используйте set)",
                "Ключи должны быть изменяемыми"
            ],
            "Сложность операций": {
                "Поиск по ключу": "O(1) в среднем",
                "Вставка": "O(1) в среднем",
                "Удаление": "O(1) в среднем"
            }
        }
    }
    
    print("=== Рекомендации по выбору коллекций ===\n")
    
    for collection_type, info in recommendations.items():
        print(f"{collection_type}:")
        
        if "Используйте когда" in info:
            print("  ✅ Используйте когда:")
            for use_case in info["Используйте когда"]:
                print(f"    • {use_case}")
        
        if "Не используйте когда" in info:
            print("  ❌ Не используйте когда:")
            for avoid_case in info["Не используйте когда"]:
                print(f"    • {avoid_case}")
        
        if "Сложность операций" in info:
            print("  ⚡ Сложность операций:")
            for operation, complexity in info["Сложность операций"].items():
                print(f"    • {operation}: {complexity}")
        
        if "Преимущества" in info:
            print("  💡 Преимущества:")
            for advantage in info["Преимущества"]:
                print(f"    • {advantage}")
        
        print()

# Примеры выбора правильной коллекции
def practical_examples():
    """Практические примеры выбора коллекций"""
    
    print("=== Практические примеры ===\n")
    
    # Пример 1: Обработка списка покупок
    print("1. Список покупок:")
    shopping_list = ["молоко", "хлеб", "яйца", "молоко"]  # Дубликаты допустимы
    print(f"   Список (с дубликатами): {shopping_list}")
    
    unique_items = set(shopping_list)  # Уникальные товары
    print(f"   Уникальные товары: {unique_items}")
    
    # Пример 2: Кэш результатов
    print("\n2. Кэш результатов вычислений:")
    calculation_cache = {}  # Словарь для кэша
    
    def cached_square(x):
        if x not in calculation_cache:
            calculation_cache[x] = x ** 2
            print(f"   Вычислено: {x}² = {x**2}")
        else:
            print(f"   Из кэша: {x}² = {calculation_cache[x]}")
        return calculation_cache[x]
    
    for num in [5, 3, 5, 7, 3]:
        cached_square(num)
    
    # Пример 3: Координаты точек
    print("\n3. Координаты точек:")
    points = [(0, 0), (1, 1), (2, 2)]  # Кортежи для неизменяемых координат
    print(f"   Точки: {points}")
    
    # Можем использовать как ключи словаря
    point_names = {(0, 0): "origin", (1, 1): "diagonal", (2, 2): "far_diagonal"}
    print(f"   Именованные точки: {point_names}")
    
    # Пример 4: Анализ посетителей
    print("\n4. Уникальные посетители:")
    daily_visitors = ["alice", "bob", "charlie", "alice", "diana", "bob"]
    unique_visitors = set(daily_visitors)  # Множество для уникальности
    visitor_count = len(unique_visitors)
    
    print(f"   Всего визитов: {len(daily_visitors)}")
    print(f"   Уникальных посетителей: {visitor_count}")
    print(f"   Список уникальных: {unique_visitors}")

if __name__ == "__main__":
    # benchmark_collections()  # Раскомментируйте для запуска бенчмарков
    collection_recommendations()
    practical_examples()
```