#python

Python предоставляет два основных типа циклов: `for` и `while`, а также множество инструментов для их эффективного использования

#### 🔹 Цикл for
Цикл `for` используется для итерации по последовательностям (списки, кортежи, строки, множества, словари) и другим итерируемым объектам.

##### 🔸 Базовый синтаксис
```python
# Итерация по списку
fruits = ["яблоко", "банан", "апельсин", "груша"]

print("Фрукты в корзине:")
for fruit in fruits:
    print(f"- {fruit}")

# Итерация по строке
word = "Python"
print(f"\nБуквы в слове '{word}':")
for letter in word:
    print(f"'{letter}'", end=" ")
print()

# Итерация по множеству
numbers = {1, 2, 3, 4, 5}
print(f"\nЧисла во множестве:")
for number in numbers:
    print(f"{number}²", end=" ")
print()

# Итерация по кортежу
coordinates = (10, 20, 30)
print(f"\nКоординаты:")
for i, coord in enumerate(coordinates):
    print(f"Ось {i}: {coord}")
```

##### 🔸 Функция range()
```python
# Основные способы использования range()

# range(stop) - от 0 до stop-1
print("Числа от 0 до 4:")
for i in range(5):
    print(i, end=" ")
print()

# range(start, stop) - от start до stop-1
print("Числа от 2 до 7:")
for i in range(2, 8):
    print(i, end=" ")
print()

# range(start, stop, step) - с шагом
print("Четные числа от 0 до 10:")
for i in range(0, 11, 2):
    print(i, end=" ")
print()

print("Нечетные числа от 1 до 10:")
for i in range(1, 11, 2):
    print(i, end=" ")
print()

# Обратный порядок
print("Обратный счет:")
for i in range(10, 0, -1):
    print(i, end=" ")
print("Пуск!")

# Отрицательные числа
print("От -5 до 5 с шагом 2:")
for i in range(-5, 6, 2):
    print(i, end=" ")
print()

# range как список (для демонстрации)
print(f"range(5) содержит: {list(range(5))}")
print(f"range(2, 8) содержит: {list(range(2, 8))}")
print(f"range(0, 10, 2) содержит: {list(range(0, 10, 2))}")
```

##### 🔸 Итерация по словарям
```python
# Различные способы итерации по словарям
student = {
    "имя": "Анна",
    "возраст": 22,
    "курс": 3,
    "специальность": "Информатика",
    "средний_балл": 4.5
}

# Итерация по ключам (по умолчанию)
print("Ключи словаря:")
for key in student:
    print(f"- {key}")

# Итерация по ключам явно
print("\nКлючи (явно):")
for key in student.keys():
    print(f"- {key}")

# Итерация по значениям
print("\nЗначения:")
for value in student.values():
    print(f"- {value}")

# Итерация по парам ключ-значение
print("\nПары ключ-значение:")
for key, value in student.items():
    print(f"{key}: {value}")

# Практический пример: форматированный вывод информации о студенте
print("\n" + "="*50)
print("ИНФОРМАЦИЯ О СТУДЕНТЕ")
print("="*50)

for field, value in student.items():
    # Красивое форматирование имен полей
    formatted_field = field.replace("_", " ").title()
    print(f"{formatted_field:.<20} {value}")

# Фильтрация при итерации
print("\nТолько числовые значения:")
for key, value in student.items():
    if isinstance(value, (int, float)):
        print(f"{key}: {value}")
```

##### 🔸 enumerate() - получение индексов
```python
# enumerate() возвращает пары (индекс, элемент)
programming_languages = ["Python", "Java", "JavaScript", "C++", "Go"]

print("Популярные языки программирования:")
for index, language in enumerate(programming_languages):
    print(f"{index + 1}. {language}")

# enumerate с начальным значением
print("\nС начальным значением 10:")
for index, language in enumerate(programming_languages, start=10):
    print(f"{index}. {language}")

# Практический пример: поиск индекса элемента
search_language = "JavaScript"
print(f"\nПоиск '{search_language}':")

for index, language in enumerate(programming_languages):
    if language == search_language:
        print(f"Найден '{search_language}' на позиции {index}")
        break
else:
    print(f"'{search_language}' не найден")

# Создание словаря индекс->элемент
indexed_dict = {index: language for index, language in enumerate(programming_languages)}
print(f"\nСловарь индекс->язык: {indexed_dict}")

# Группировка элементов по четности индекса
even_indexed = []
odd_indexed = []

for index, language in enumerate(programming_languages):
    if index % 2 == 0:
        even_indexed.append(language)
    else:
        odd_indexed.append(language)

print(f"Четные позиции: {even_indexed}")
print(f"Нечетные позиции: {odd_indexed}")
```

##### 🔸 zip() - параллельная итерация
```python
# zip() объединяет несколько итерируемых объектов
names = ["Анна", "Борис", "Виктор", "Галина"]
ages = [25, 30, 28, 32]
cities = ["Москва", "СПб", "Казань", "Екатеринбург"]

print("Информация о людях:")
for name, age, city in zip(names, ages, cities):
    print(f"{name}, {age} лет, {city}")

# zip останавливается на самой короткой последовательности
short_list = ["A", "B"]
long_list = [1, 2, 3, 4, 5]

print("\nСамая короткая определяет длину:")
for letter, number in zip(short_list, long_list):
    print(f"{letter}: {number}")

# itertools.zip_longest для работы с разными длинами
from itertools import zip_longest

print("\nС zip_longest (заполнение None):")
for letter, number in zip_longest(short_list, long_list):
    print(f"{letter}: {number}")

print("\nС zip_longest (заполнение 'N/A'):")
for letter, number in zip_longest(short_list, long_list, fillvalue="N/A"):
    print(f"{letter}: {number}")

# Создание словаря из двух списков
subjects = ["Математика", "Физика", "Химия", "История"]
grades = [5, 4, 5, 3]

grade_dict = dict(zip(subjects, grades))
print(f"\nОценки: {grade_dict}")

# Транспонирование матрицы с помощью zip
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

print("\nОригинальная матрица:")
for row in matrix:
    print(row)

transposed = list(zip(*matrix))
print("\nТранспонированная матрица:")
for row in transposed:
    print(list(row))

# Параллельная обработка нескольких списков
prices_rub = [1000, 1500, 2000, 500]
exchange_rate = 0.013  # рубль к доллару

products = ["Телефон", "Ноутбук", "Планшет", "Наушники"]

print("\nПрайс-лист в двух валютах:")
for product, price_rub in zip(products, prices_rub):
    price_usd = price_rub * exchange_rate
    print(f"{product:.<15} {price_rub:>6} ₽ ({price_usd:>6.2f} $)")
```

##### 🔸 Вложенные циклы
```python
# Вложенные циклы для работы с многомерными структурами

# Таблица умножения
print("Таблица умножения 5x5:")
print("   ", end="")
for j in range(1, 6):
    print(f"{j:4}", end="")
print()

for i in range(1, 6):
    print(f"{i:2} ", end="")
    for j in range(1, 6):
        print(f"{i*j:4}", end="")
    print()

# Генерация координатной сетки
print("\nКоординатная сетка 3x3:")
coordinates = []
for x in range(3):
    for y in range(3):
        coordinates.append((x, y))
        print(f"({x},{y})", end=" ")
    print()

print(f"Всего координат: {len(coordinates)}")

# Поиск в двумерном массиве
matrix = [
    [1, 5, 9],
    [2, 6, 10],
    [3, 7, 11],
    [4, 8, 12]
]

search_value = 7
print(f"\nПоиск значения {search_value} в матрице:")

found = False
for i, row in enumerate(matrix):
    for j, value in enumerate(row):
        if value == search_value:
            print(f"Найдено значение {search_value} в позиции [{i}][{j}]")
            found = True
            break
    if found:  # Выход из внешнего цикла
        break

if not found:
    print(f"Значение {search_value} не найдено")

# Создание шахматной доски
print("\nШахматная доска 8x8:")
for row in range(8):
    for col in range(8):
        if (row + col) % 2 == 0:
            print("⬜", end="")
        else:
            print("⬛", end="")
    print()

# Генерация всех комбинаций
colors = ["красный", "синий", "зеленый"]
sizes = ["S", "M", "L"]

print("\nВсе комбинации цвет-размер:")
for color in colors:
    for size in sizes:
        print(f"{color} {size}")

# Более эффективный способ - itertools.product
from itertools import product

print("\nТо же с itertools.product:")
for color, size in product(colors, sizes):
    print(f"{color} {size}")
```
---
#### 🔹 Цикл while
Цикл `while` выполняется, пока условие истинно. Он подходит для случаев, когда количество итераций заранее неизвестно.

##### 🔸  Базовое использование
```python
# Простой счетчик
print("Счет от 1 до 5:")
count = 1
while count <= 5:
    print(f"Счет: {count}")
    count += 1

print("Готово!")

# Ввод данных до получения корректного значения
def get_positive_number():
    """Запрашивает положительное число у пользователя"""
    while True:
        try:
            value = float(input("Введите положительное число: "))
            if value > 0:
                return value
            else:
                print("Число должно быть положительным!")
        except ValueError:
            print("Пожалуйста, введите корректное число!")

# number = get_positive_number()  # Раскомментируйте для интерактивного ввода
# print(f"Вы ввели: {number}")

# Обработка списка до выполнения условия
numbers = [1, 3, 5, 7, 2, 9, 11, 4, 13]
print(f"Исходный список: {numbers}")

index = 0
print("Обрабатываем числа до первого четного:")
while index < len(numbers):
    current = numbers[index]
    print(f"Обрабатываем: {current}")
    
    if current % 2 == 0:
        print(f"Найдено первое четное число: {current}")
        break
    
    index += 1
else:
    print("Четных чисел не найдено")

# Алгоритм Евклида (НОД)
def gcd(a, b):
    """Вычисляет наибольший общий делитель"""
    print(f"Находим НОД чисел {a} и {b}:")
    
    while b != 0:
        print(f"  {a} = {b} * {a // b} + {a % b}")
        a, b = b, a % b
    
    print(f"НОД = {a}")
    return a

result = gcd(48, 18)

# Последовательность Коллатца
def collatz_sequence(n):
    """Генерирует последовательность Коллатца"""
    sequence = [n]
    steps = 0
    
    while n != 1:
        if n % 2 == 0:
            n = n // 2
        else:
            n = 3 * n + 1
        
        sequence.append(n)
        steps += 1
        
        # Защита от бесконечного цикла
        if steps > 1000:
            print("Слишком много шагов, прерываем...")
            break
    
    return sequence, steps

start_number = 7
seq, step_count = collatz_sequence(start_number)
print(f"\nПоследовательность Коллатца для {start_number}:")
print(f"Последовательность: {seq}")
print(f"Количество шагов: {step_count}")
```

##### 🔸 Продвинутые паттерны while
```python
# Обработка данных порциями
def process_data_in_batches(data, batch_size=3):
    """Обрабатывает данные порциями"""
    index = 0
    batch_number = 1
    
    while index < len(data):
        # Получаем текущую порцию
        batch = data[index:index + batch_size]
        
        print(f"Порция {batch_number}: {batch}")
        
        # Имитация обработки
        processed_batch = [x * 2 for x in batch]
        print(f"Обработано: {processed_batch}")
        
        index += batch_size
        batch_number += 1
        print()

# Тестируем обработку порциями
large_dataset = list(range(1, 16))  # 1-15
print("Обработка данных порциями:")
process_data_in_batches(large_dataset, batch_size=4)

# Поиск с условием выхода
def find_with_condition(data, condition_func, max_attempts=None):
    """Ищет элемент по условию с ограничением попыток"""
    index = 0
    attempts = 0
    
    while index < len(data):
        attempts += 1
        current = data[index]
        
        print(f"Попытка {attempts}: проверяем {current}")
        
        if condition_func(current):
            print(f"Найдено: {current} на позиции {index}")
            return current, index
        
        if max_attempts and attempts >= max_attempts:
            print(f"Достигнуто максимальное количество попыток: {max_attempts}")
            break
        
        index += 1
    
    return None, -1

# Тестируем поиск
test_data = [1, 3, 7, 12, 15, 20, 25, 30]
condition = lambda x: x > 10 and x % 5 == 0  # Больше 10 и кратно 5

result, position = find_with_condition(test_data, condition, max_attempts=5)
if result:
    print(f"Результат поиска: {result} на позиции {position}")
else:
    print("Элемент не найден")

# Интерактивное меню
def interactive_menu():
    """Интерактивное меню с циклом while"""
    menu_options = {
        "1": "Показать время",
        "2": "Показать дату", 
        "3": "Вычислить квадрат числа",
        "4": "Показать случайное число",
        "0": "Выход"
    }
    
    while True:
        print("\n" + "="*30)
        print("ГЛАВНОЕ МЕНЮ")
        print("="*30)
        
        for key, description in menu_options.items():
            print(f"{key}. {description}")
        
        choice = input("\nВыберите опцию (0-4): ").strip()
        
        if choice == "0":
            print("До свидания!")
            break
        elif choice == "1":
            from datetime import datetime
            print(f"Текущее время: {datetime.now().strftime('%H:%M:%S')}")
        elif choice == "2":
            from datetime import date
            print(f"Текущая дата: {date.today().strftime('%d.%m.%Y')}")
        elif choice == "3":
            try:
                number = float(input("Введите число: "))
                print(f"Квадрат числа {number}: {number**2}")
            except ValueError:
                print("Некорректное число!")
        elif choice == "4":
            import random
            print(f"Случайное число: {random.randint(1, 100)}")
        else:
            print("Некорректная опция! Попробуйте снова.")
        
        input("\nНажмите Enter для продолжения...")

# interactive_menu()  # Раскомментируйте для запуска интерактивного меню

# Симуляция игры
def number_guessing_game():
    """Игра 'угадай число'"""
    import random
    
    secret_number = random.randint(1, 100)
    attempts = 0
    max_attempts = 7
    
    print("🎯 Игра 'Угадай число'!")
    print(f"Я загадал число от 1 до 100. У вас {max_attempts} попыток.")
    
    while attempts < max_attempts:
        try:
            guess = int(input(f"\nПопытка {attempts + 1}: "))
            attempts += 1
            
            if guess == secret_number:
                print(f"🎉 Поздравляем! Вы угадали за {attempts} попыток!")
                return
            elif guess < secret_number:
                print("📈 Загаданное число больше")
            else:
                print("📉 Загаданное число меньше")
            
            remaining = max_attempts - attempts
            if remaining > 0:
                print(f"Осталось попыток: {remaining}")
            
        except ValueError:
            print("Пожалуйста, введите целое число!")
            attempts -= 1  # Не считаем неверный ввод
    
    print(f"\n😞 Игра окончена! Загаданное число было: {secret_number}")

# number_guessing_game()  # Раскомментируйте для игры
```
---
#### 🔹 Управление циклами: break, continue, else

##### 🔸 break - прерывание цикла
```python
# break в цикле for
print("Поиск первого числа больше 50:")
numbers = [10, 25, 30, 45, 55, 60, 75, 80]

for num in numbers:
    print(f"Проверяем: {num}")
    if num > 50:
        print(f"Найдено первое число больше 50: {num}")
        break
    print(f"{num} не подходит, продолжаем...")
```

##### 🔸 continue - пропуск итерации
```python
# continue в цикле for
print("Обработка только положительных чисел:")
numbers = [-2, -1, 0, 1, 2, 3, -5, 4, 5]

for num in numbers:
    if num <= 0:
        print(f"Пропускаем {num} (не положительное)")
        continue
    
    # Эта часть выполняется только для положительных чисел
    square = num ** 2
    print(f"{num}² = {square}")
```

##### 🔸 else в циклах
```python
# else выполняется, если цикл завершился естественно (без break)

print("Поиск простого числа:")
def is_prime(n):
    """Проверяет, является ли число простым"""
    if n < 2:
        return False
    
    print(f"Проверяем делители числа {n}:")
    
    for i in range(2, int(n**0.5) + 1):
        print(f"  Проверяем деление на {i}: {n} % {i} = {n % i}")
        if n % i == 0:
            print(f"  {n} делится на {i}, значит не простое")
            return False
    else:
        # Этот блок выполняется, если цикл завершился без break
        print(f"  Делителей не найдено, {n} - простое число")
        return True
```

#### 🔹 Продвинутые техники работы с циклами
##### 🔸 itertools - мощные инструменты для итерации
```python
from itertools import (
    count, cycle, repeat, chain, combinations, permutations, 
    product, groupby, islice, takewhile, dropwhile, compress
)

print("=== itertools - продвинутые итераторы ===")

# count() - бесконечный счетчик
print("count() - бесконечный счетчик:")
counter = count(start=10, step=3)  # Начинаем с 10, шаг 3
for i, value in enumerate(counter):
    if i >= 5:
        break
    print(value, end=" ")
print()

# cycle() - бесконечное повторение последовательности
print("\ncycle() - циклическое повторение:")
colors = cycle(['красный', 'зеленый', 'синий'])
for i, color in enumerate(colors):
    if i >= 8:
        break
    print(f"Цвет {i}: {color}")

# repeat() - повторение одного элемента
print("\nrepeat() - повторение элемента:")
repeated = repeat('Python', 5)
print(list(repeated))

# chain() - объединение итераторов
print("\nchain() - объединение последовательностей:")
list1 = [1, 2, 3]
list2 = ['a', 'b', 'c']
list3 = [10, 20, 30]
chained = chain(list1, list2, list3)
print(f"Объединенная последовательность: {list(chained)}")

# combinations() - комбинации без повторений
print("\ncombinations() - сочетания:")
items = ['A', 'B', 'C', 'D']
for combo in combinations(items, 2):
    print(combo)

# permutations() - размещения
print("\npermutations() - размещения:")
small_items = ['X', 'Y', 'Z']
for perm in permutations(small_items, 2):
    print(perm)

# product() - декартово произведение
print("\nproduct() - декартово произведение:")
colors = ['красный', 'синий']
sizes = ['S', 'M', 'L']
for color, size in product(colors, sizes):
    print(f"{color} {size}")

# groupby() - группировка соседних элементов
print("\ngroupby() - группировка:")
data = [
    ('A', 1), ('A', 2), ('B', 3), ('B', 4), ('B', 5), ('C', 6)
]

for key, group in groupby(data, key=lambda x: x[0]):
    group_list = list(group)
    print(f"Группа {key}: {group_list}")

# islice() - срезы итераторов
print("\nislice() - срезы итераторов:")
numbers = count(1)  # Бесконечная последовательность
slice_result = list(islice(numbers, 5, 15, 2))  # [старт:стоп:шаг]
print(f"islice(count(1), 5, 15, 2): {slice_result}")

# takewhile() и dropwhile()
print("\ntakewhile() и dropwhile():")
numbers = [1, 3, 5, 8, 9, 11, 13, 2, 4]

taken = list(takewhile(lambda x: x < 10, numbers))
dropped = list(dropwhile(lambda x: x < 10, numbers))

print(f"Исходная последовательность: {numbers}")
print(f"takewhile(x < 10): {taken}")
print(f"dropwhile(x < 10): {dropped}")

# compress() - фильтрация по маске
print("\ncompress() - фильтрация по маске:")
data = ['a', 'b', 'c', 'd', 'e', 'f']
selectors = [1, 0, 1, 0, 1, 1]  # 1 = взять, 0 = пропустить
filtered = list(compress(data, selectors))
print(f"Данные: {data}")
print(f"Маска: {selectors}")
print(f"Результат: {filtered}")
```

##### 🔸 Пользовательские итераторы
```python
# Создание собственных итераторов
print("\n=== Пользовательские итераторы ===")

class CountDown:
    """Итератор обратного отсчета"""
    
    def __init__(self, start):
        self.start = start
        self.current = start
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        
        self.current -= 1
        return self.current + 1

# Использование пользовательского итератора
print("Обратный отсчет с 5:")
for num in CountDown(5):
    print(f"{num}...", end=" ")
print("Пуск!")

class FibonacciIterator:
    """Итератор для чисел Фибоначчи"""
    
    def __init__(self, max_count):
        self.max_count = max_count
        self.count = 0
        self.a, self.b = 0, 1
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.count >= self.max_count:
            raise StopIteration
        
        if self.count == 0:
            self.count += 1
            return self.a
        elif self.count == 1:
            self.count += 1
            return self.b
        else:
            self.a, self.b = self.b, self.a + self.b
            self.count += 1
            return self.b

print(f"\nПервые 10 чисел Фибоначчи:")
fib_iter = FibonacciIterator(10)
for fib_num in fib_iter:
    print(fib_num, end=" ")
print()

# Итератор с состоянием
class StatefulIterator:
    """Итератор, который помнит статистику итерации"""
    
    def __init__(self, data):
        self.data = data
        self.index = 0
        self.stats = {
            "iterations": 0,
            "sum": 0,
            "min": float('inf'),
            "max": float('-inf')
        }
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.index >= len(self.data):
            raise StopIteration
        
        value = self.data[self.index]
        self.index += 1
        
        # Обновляем статистику
        self.stats["iterations"] += 1
        self.stats["sum"] += value
        self.stats["min"] = min(self.stats["min"], value)
        self.stats["max"] = max(self.stats["max"], value)
        
        return value
    
    def get_stats(self):
        """Возвращает статистику итерации"""
        if self.stats["iterations"] > 0:
            avg = self.stats["sum"] / self.stats["iterations"]
            return {**self.stats, "average": avg}
        return self.stats

# Тестируем итератор со статистикой
data = [10, 5, 8, 15, 3, 12, 7]
stat_iter = StatefulIterator(data)

print(f"\nИтерация с подсчетом статистики:")
result = []
for value in stat_iter:
    result.append(value)
    print(f"Значение: {value}")

stats = stat_iter.get_stats()
print(f"\nСтатистика итерации:")
for key, value in stats.items():
    print(f"  {key}: {value}")
```

##### 🔸 Оптимизация производительности циклов
```python
import time
import random

print("=== Оптимизация производительности циклов ===")

def benchmark_function(func, *args, iterations=1):
    """Измеряет время выполнения функции"""
    start_time = time.time()
    
    for _ in range(iterations):
        result = func(*args)
    
    end_time = time.time()
    execution_time = (end_time - start_time) / iterations
    
    return result, execution_time

# Сравнение различных способов создания списков
def create_with_append():
    """Создание списка через append"""
    result = []
    for i in range(10000):
        result.append(i * 2)
    return result

def create_with_list_comp():
    """Создание списка через list comprehension"""
    return [i * 2 for i in range(10000)]

def create_with_map():
    """Создание списка через map"""
    return list(map(lambda x: x * 2, range(10000)))

# Бенчмарк создания списков
print("Сравнение методов создания списков (10000 элементов):")

methods = [
    ("append()", create_with_append),
    ("list comprehension", create_with_list_comp),
    ("map()", create_with_map)
]

for name, method in methods:
    result, exec_time = benchmark_function(method, iterations=100)
    print(f"{name:20}: {exec_time:.6f} секунд")

# Оптимизация поиска
def linear_search(data, target):
    """Линейный поиск"""
    for i, item in enumerate(data):
        if item == target:
            return i
    return -1

def linear_search_optimized(data, target):
    """Оптимизированный линейный поиск"""
    try:
        return data.index(target)
    except ValueError:
        return -1

def binary_search(data, target):
    """Бинарный поиск (для отсортированных данных)"""
    left, right = 0, len(data) - 1
    
    while left <= right:
        mid = (left + right) // 2
        if data[mid] == target:
            return mid
        elif data[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1

# Тестирование поиска
print("\nСравнение методов поиска:")
test_data = sorted(random.sample(range(100000), 50000))
search_target = random.choice(test_data)

search_methods = [
    ("Линейный поиск", linear_search),
    ("Линейный (оптимизированный)", linear_search_optimized),
    ("Бинарный поиск", binary_search)
]

for name, method in search_methods:
    result, exec_time = benchmark_function(method, test_data, search_target, iterations=1000)
    print(f"{name:25}: {exec_time:.8f} секунд, результат: {result}")

# Оптимизация работы со словарями
def count_with_dict_get():
    """Подсчет с использованием dict.get()"""
    text = "python is great python is powerful python is simple" * 1000
    words = text.split()
    
    word_count = {}
    for word in words:
        word_count[word] = word_count.get(word, 0) + 1
    
    return word_count

def count_with_dict_setdefault():
    """Подсчет с использованием dict.setdefault()"""
    text = "python is great python is powerful python is simple" * 1000
    words = text.split()
    
    word_count = {}
    for word in words:
        word_count.setdefault(word, 0)
        word_count[word] += 1
    
    return word_count

def count_with_defaultdict():
    """Подсчет с использованием defaultdict"""
    from collections import defaultdict
    
    text = "python is great python is powerful python is simple" * 1000
    words = text.split()
    
    word_count = defaultdict(int)
    for word in words:
        word_count[word] += 1
    
    return dict(word_count)

def count_with_counter():
    """Подсчет с использованием Counter"""
    from collections import Counter
    
    text = "python is great python is powerful python is simple" * 1000
    words = text.split()
    
    return dict(Counter(words))

print("\nСравнение методов подсчета слов:")
counting_methods = [
    ("dict.get()", count_with_dict_get),
    ("dict.setdefault()", count_with_dict_setdefault), 
    ("defaultdict", count_with_defaultdict),
    ("Counter", count_with_counter)
]

for name, method in counting_methods:
    result, exec_time = benchmark_function(method, iterations=100)
    print(f"{name:20}: {exec_time:.6f} секунд")

# Оптимизация вложенных циклов
def nested_loops_naive():
    """Наивная реализация вложенных циклов"""
    result = []
    for i in range(100):
        for j in range(100):
            if i * j % 7 == 0:  # Дорогая операция в условии
                result.append((i, j))
    return result

def nested_loops_optimized():
    """Оптимизированная версия"""
    result = []
    for i in range(100):
        i_mod_7 = i % 7  # Вычисляем один раз
        for j in range(100):
            if (i_mod_7 * (j % 7)) % 7 == 0:  # Используем предвычисленное значение
                result.append((i, j))
    return result

print("\nСравнение вложенных циклов:")
nested_methods = [
    ("Наивный подход", nested_loops_naive),
    ("Оптимизированный", nested_loops_optimized)
]

for name, method in nested_methods:
    result, exec_time = benchmark_function(method, iterations=10)
    print(f"{name:20}: {exec_time:.6f} секунд, найдено пар: {len(result)}")

# Рекомендации по оптимизации
print("\n" + "="*60)
print("РЕКОМЕНДАЦИИ ПО ОПТИМИЗАЦИИ ЦИКЛОВ")
print("="*60)

recommendations = [
    "1. Используйте list comprehensions вместо циклов для создания списков",
    "2. Применяйте встроенные функции (map, filter, sum, any, all)",
    "3. Избегайте повторных вычислений в циклах",
    "4. Используйте enumerate() вместо range(len())",
    "5. Применяйте генераторы для больших данных",
    "6. Выносите неизменяемые операции из циклов",
    "7. Используйте подходящие структуры данных (set для поиска)",
    "8. Применяйте itertools для сложных операций",
    "9. Рассмотрите numpy для численных вычислений",
    "10. Профилируйте код перед оптимизацией"
]

for recommendation in recommendations:
    print(f"  {recommendation}")

# Практические примеры оптимизации
print(f"\nПрактические примеры:")

# Неэффективно
def inefficient_example(data):
    result = []
    for i in range(len(data)):  # Используется range(len())
        if data[i] % 2 == 0:
            result.append(data[i] ** 2)
    return result

# Эффективно
def efficient_example(data):
    return [x ** 2 for x in data if x % 2 == 0]

test_data = list(range(10000))
_, inefficient_time = benchmark_function(inefficient_example, test_data)
_, efficient_time = benchmark_function(efficient_example, test_data)

print(f"Неэффективный код: {inefficient_time:.6f} секунд")
print(f"Эффективный код:   {efficient_time:.6f} секунд")
print(f"Ускорение в {inefficient_time/efficient_time:.1f} раз")
```