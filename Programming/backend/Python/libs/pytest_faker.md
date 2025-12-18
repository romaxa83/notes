#python #testing

- https://habr.com/ru/companies/beget/articles/948806/

#### 🔹 Assert

```python
response = client.post("/books", json=_data)  

# проверка статуса ответа
assert response.status_code == status.HTTP_201_CREATED  
  
data = response.json()

# проверка значение
assert data["title"] == _data["title"]

# проверка наличие значение в результате
assert "id" in data

# значение не пустое
assert data["created_at"] is not None
```

#### 🔹 Команды `pytest`

```bash
# Все тесты  
pytest  
  
# С отчётом о покрытии  
pytest --cov=src tests/  
  
# Конкретный файл  
pytest tests/test_authors.py  
  
# Конкретный класс тестов  
pytest tests/test_authors.py::TestCreateAuthor  
  
# С выводом print'ов  
pytest -s tests/test_authors.py
```

##### 🔸 Уровень детализации (verbosity)
```bash
pytest -v         # Подробный вывод (показывает имена тестов)
pytest -vv        # Очень подробный (показывает полные сравнения при assert)
pytest -q         # Тихий режим (минимум информации)
pytest -qq        # Очень тихий режим
```

##### 🔸 Отображение print() в тестах
```bash
pytest -s         # Показывать print() и другой stdout
pytest --capture=no  # То же самое
```

##### 🔸 Красивое форматирование ошибок
```bash
pytest --tb=short     # Короткий traceback
pytest --tb=line      # Одна строка на ошибку
pytest --tb=no        # Без traceback
pytest --tb=long      # Полный traceback (по умолчанию)
pytest --tb=native    # Стандартный Python traceback
```

##### 🔸 Показать локальные переменные при ошибках
```bash
pytest -l
pytest --showlocals
```

##### 🔸 Цветной вывод
```bash
pytest --color=yes    # Всегда цветной
pytest --color=no     # Без цветов
pytest --color=auto   # Автоматически (по умолчанию)
```

##### 🔸 Показать diff при сравнениях
```bash
pytest -vv            # Показывает полные различия в assert
pytest --maxfail=1    # Остановиться после первой ошибки
```

##### 🔸 Показать самые медленные тесты
```bash
pytest --durations=10   # Показать 10 самых медленных тестов
pytest --durations=0    # Показать время всех тестов
```

##### 🔸 Показать setup/teardown
```bash
# Разработка - максимум информации
pytest -vvs --tb=short -l

# CI/CD - чистый вывод с важной информацией
pytest -v --tb=line --color=yes

# Отладка конкретного теста
pytest -vvs --tb=short -k "test_name" --pdb

# Производительность
pytest -v --durations=10

# Красивый отчет с процентами
pytest -v --tb=short --cov=src --cov-report=term-missing
```

##### 🔸 Плагины для красивого вывода
```bash
# Установка
pip install pytest-sugar    # Прогресс-бар и красивый вывод
pip install pytest-clarity  # Лучшие diff при ошибках
pip install pytest-icdiff   # Цветной diff
pip install pytest-instafail # Показывать ошибки сразу

# Использование
pytest --sugar
```

##### 🔸 Рекомендую для повседневной разработк
```bash
pytest -vvs --tb=short -l
```
--- 
#### 🔹 Faker
> библиотека для генерации фейковых данных

```bash
pip install faker
```

##### 🔸 Примеры использования в тестах
```python
from faker import Faker

fake = Faker()

# Текст
fake.name()                     # "John Doe"
fake.first_name()              # "John"
fake.last_name()               # "Doe"
fake.email()                   # "john@example.com"
fake.url()                     # "https://example.com"
fake.text()                    # "Lorem ipsum..."
fake.sentence()                # "This is a sentence."
fake.paragraph()               # Много текста
fake.word()                    # "word"

# Числа
fake.random_int(min=0, max=100)
fake.random_digit()            # 0-9

# Дата и время
fake.date()                    # "2023-01-15"
fake.date_time()               # datetime object
fake.future_date()
fake.past_date()
fake.time()                    # "12:30:45"

# Адреса
fake.address()
fake.city()
fake.country()

# Локализация
fake_ru = Faker('ru_RU')       # Русские данные
fake_ru.name()                 # "Иван Петров"
```