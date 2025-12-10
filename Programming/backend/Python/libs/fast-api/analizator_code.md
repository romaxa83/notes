#python #fast-api #pretty-code

#### 🔹 Установка инструментов

```bash
pip install pylint flake8 black isort mypy
```

- **pylint** — глубокий анализ кода
- **flake8** — стиль кода и ошибки
- **black** — автоматическое форматирование
- **isort** — сортировка импортов
- **mypy** — проверка типов
---
#### 🔹  Создаем конфиги

```bash
touch {.pylintrc,.flake8,pyproject.toml}
```

==.flake8==
```ini
[flake8]  
max-line-length = 120  
exclude =  
    venv,  
    __pycache__,  
    .git,  
    alembic,  
    .venv  
ignore =  
    E203,  
    W503
```

==pyproject.toml==
```toml
[tool.black]  
line-length = 120  
target-version = ['py313']  
include = '\.pyi?$'  
exclude = '''  
/(  
    \.git  | \.venv  | venv  | __pycache__  | alembic)/  
'''  
  
[tool.isort]  
profile = "black"  
line_length = 120  
skip_gitignore = true  
skip = ["venv", ".venv", "__pycache__", "alembic"]  
  
[tool.mypy]  
python_version = "3.13"  
warn_return_any = true  
warn_unused_configs = true  
ignore_missing_imports = true  
exclude = ["venv", "alembic"]  
  
[[tool.mypy.overrides]]  
module = "sqlalchemy.*"  
ignore_missing_imports = true
```

==.pylintrc== (Основные секции)
```ini
[MASTER]  
# Игнорировать папки  
ignore=venv,__pycache__,.git,alembic  
  
# Максимальная длина строки  
max-line-length=120  
  
[MESSAGES CONTROL]  
# Отключить определённые сообщения  
disable=  
    C0111,  # missing-docstring (нет docstring)
    C0103,  # invalid-name (неправильное имя переменной)
    R0913   # too-many-arguments (слишком много аргументов)
  
[DESIGN]  
# Ограничения на сложность кода 
max-args=6  # Макс аргументов в функции
max-attributes=7  # Макс атрибутов в классе
max-locals=15             # Макс локальных переменных  
max-branches=12           # Макс веток в функции  
max-statements=50         # Макс строк в функции
  
[VARIABLES]  
# Минимальная длина имени переменной  
min-name-length=2

[BASIC]  
# Правила именования  
good-names=i,j,k,ex,Run,_  # Разрешённые однобуквенные имена  
bad-names=foo,bar,baz       # Запрещённые имена
```
---
#### 🔹 Создайте скрипт для анализа

```bash
#!/bin/bash

echo "🔍 Проверка кода с помощью pylint..."
pylint src/ --fail-under=8.0 || true

echo ""
echo "📋 Проверка стиля с помощью flake8..."
flake8 src/

echo ""
echo "🎨 Проверка форматирования с помощью black..."
black --check src/ || true

echo ""
echo "📦 Проверка импортов с помощью isort..."
isort --check-only src/ || true

echo ""
echo "📝 Проверка типов с помощью mypy..."
mypy src/ || true

echo ""
echo "✅ Анализ завершён!"
```

==Дайте права на выполнение==
```bash
chmod +x scripts/lint.sh
```
---
#### 🔹 Запуск анализа

```bash
# Все проверки сразу
./scripts/lint.sh

# Или отдельно:

# Pylint (самый строгий)
pylint src/ --fail-under=8.0

# Flake8 (стиль)
flake8 src/

# Black (форматирование)
black src/

# isort (импорты)
isort src/

# mypy (типы)
mypy src/
```
---
#### 🔹 Автоматическое исправление

```ini
# Black автоматически форматирует код
black src/

# isort автоматически сортирует импорты
isort src/
```

==Создайте скрипт для автоисправления==
```bash
#!/bin/bash

echo "🎨 Форматирование кода с Black..."
black src/

echo ""
echo "📦 Сортировка импортов с isort..."
isort src/

echo ""
echo "✅ Код отформатирован!"
```

```bash
chmod +x scripts/format.sh  
./scripts/format.sh
```

> сначала запускаем форматирование а потом проверку

```bash
./scripts/format.sh
./scripts/lint.sh
```