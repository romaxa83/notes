#python

#### 🔹 Оператор if

Основная структура условных операторов в Python построена на ключевых словах `if`, `elif`, и `else`.
```python
# Базовая структура if
age = 18

if age >= 18:
    print("Вы совершеннолетний")

# if-else
if age >= 18:
    print("Вы совершеннолетний")
else:
    print("Вы несовершеннолетний")

# if-elif-else
score = 85

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
elif score >= 60:
    grade = "D"
else:
    grade = "F"

print(f"Ваша оценка: {grade}")
```

##### 🔸 Логические операторы
```python
# Логические операторы: and, or, not
age = 25
has_license = True
has_car = False

# and - все условия должны быть True
if age >= 18 and has_license:
    print("Можете водить машину")

# or - хотя бы одно условие должно быть True
if has_license or age >= 21:
    print("Можете арендовать машину")

# not - инверсия логического значения
if not has_car:
    print("Нужно купить машину")

# Комбинирование операторов
if (age >= 18 and has_license) or (age >= 21):
    print("Условие выполнено")

# Приоритет операторов
# not имеет наивысший приоритет
# затем and
# затем or
result = not False or True and False  # True
print(result)  # True (эквивалент: (not False) or (True and False))
```

##### 🔸 Операторы сравнения
```python
a = 10
b = 20
c = 10

# Базовые операторы сравнения
print(a == b)   # False (равно)
print(a != b)   # True (не равно)
print(a < b)    # True (меньше)
print(a <= c)   # True (меньше или равно)
print(b > a)    # True (больше)
print(b >= a)   # True (больше или равно)

# Цепочки сравнений (уникальная особенность Python)
x = 15
if 10 < x < 20:  # Эквивалент: 10 < x and x < 20
    print("x находится между 10 и 20")

if 0 <= score <= 100:
    print("Корректная оценка")

# Проверка принадлежности
numbers = [1, 2, 3, 4, 5]
if 3 in numbers:
    print("3 есть в списке")

if 6 not in numbers:
    print("6 нет в списке")

# Проверка идентичности
list1 = [1, 2, 3]
list2 = [1, 2, 3]
list3 = list1

print(list1 == list2)  # True (равны по значению)
print(list1 is list2)  # False (разные объекты)
print(list1 is list3)  # True (один и тот же объект)
print(list1 is not list2)  # True
```

##### 🔸 Оператор pass
```python
# pass - пустой оператор, заглушка
def placeholder_function():
    pass  # Ничего не делает, но синтаксически корректно

class EmptyClass:
    pass  # Пустой класс

# Использование pass в условиях
user_role = "admin"

if user_role == "admin":
    pass  # TODO: Реализовать админские функции
elif user_role == "user":
    print("Обычный пользователь")
else:
    print("Неизвестная роль")
```
---
#### 🔹 Истинность и ложность (Truthiness)
##### 🔸 Falsy значения
```python
# В Python следующие значения считаются ложными (falsy):
falsy_values = [
    False,      # Логическое False
    None,       # None
    0,          # Ноль (int)
    0.0,        # Ноль (float)
    0j,         # Ноль (complex)
    "",         # Пустая строка
    [],         # Пустой список
    (),         # Пустой кортеж
    {},         # Пустой словарь
    set(),      # Пустое множество
    frozenset() # Пустое неизменяемое множество
]

for value in falsy_values:  
    if not value:  
        print(f"{repr(value)} является falsy")
```

##### 🔸 Truthy значения
```python
# Все остальные значения считаются истинными (truthy)
truthy_values = [
    True,           # Логическое True
    1,              # Ненулевое число
    -1,             # Отрицательное число
    3.14,           # Ненулевое float
    "hello",        # Непустая строка
    " ",            # Строка с пробелом
    [1, 2, 3],      # Непустой список
    {"key": "value"}, # Непустой словарь
    (1,),           # Непустой кортеж
]

for value in truthy_values:  
    if value:  
        print(f"{repr(value)} является truthy")
```
---
#### 🔹 Тернарный оператор
```python
# Тернарный оператор (условное выражение)
# Синтаксис: value_if_true if condition else value_if_false

age = 20
status = "adult" if age >= 18 else "minor"
print(status)  # adult

# Сравнение с обычным if-else
# Обычный способ:
if age >= 18:
    status = "adult"
else:
    status = "minor"

# Тернарный оператор:
status = "adult" if age >= 18 else "minor"

# Вложенные тернарные операторы (не рекомендуется для сложных случаев)
score = 85
grade = "A" if score >= 90 else "B" if score >= 80 else "C" if score >= 70 else "F"
print(grade)  # B

# Лучше использовать обычный if-elif-else для сложных случаев
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"

# Практические примеры тернарного оператора
numbers = [1, 2, 3, 4, 5]
result = "not empty" if numbers else "empty"

# В функциях
def get_greeting(name):
    return f"Hello, {name}!" if name else "Hello, stranger!"

# В list comprehensions
numbers = [-2, -1, 0, 1, 2]
abs_numbers = [x if x >= 0 else -x for x in numbers]
print(abs_numbers)  # [2, 1, 0, 1, 2]
```
---
#### 🔹 Match-Case (Python 3.10+)
```python
# Новый синтаксис match-case (доступен с Python 3.10)
def handle_http_status(status_code):
    match status_code:
        case 200:
            return "OK"
        case 400:
            return "Bad Request"
        case 401:
            return "Unauthorized"
        case 403:
            return "Forbidden"
        case 404:
            return "Not Found"
        case 500:
            return "Internal Server Error"
        case _:  # wildcard pattern
            return f"Unknown status: {status_code}"

# Сопоставление с условиями (guards)
def categorize_number(x):
    match x:
        case n if n < 0:
            return "Отрицательное"
        case 0:
            return "Ноль"
        case n if n > 0 and n < 10:
            return "Малое положительное"
        case n if n >= 10:
            return "Большое положительное"

# Сопоставление с коллекциями
def analyze_list(data):
    match data:
        case []:
            return "Пустой список"
        case [x]:
            return f"Один элемент: {x}"
        case [x, y]:
            return f"Два элемента: {x}, {y}"
        case [x, *rest]:
            return f"Первый элемент: {x}, остальных: {len(rest)}"
        case _:
            return "Не список"

# Сопоставление со словарями
def process_user_data(user_info):
    match user_info:
        case {"name": str(name), "age": int(age)} if age >= 18:
            return f"Взрослый: {name}, {age} лет"
        case {"name": str(name), "age": int(age)} if age < 18:
            return f"Несовершеннолетний: {name}, {age} лет"
        case {"name": str(name)}:
            return f"Пользователь без возраста: {name}"
        case _:
            return "Некорректные данные пользователя"
```