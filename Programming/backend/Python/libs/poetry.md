#python #package

#### 🔹 Poetry

- https://habr.com/ru/articles/969296/

Современный менеджер зависимостей и сборки:
```bash
# Установка poetry
curl -sSL https://install.python-poetry.org | python3 -

# Создание нового проекта
poetry new myproject

# Инициализация в существующем проекте
poetry init

# покажет все зависимости и их зависимости
poetry show --tree

# Установка зависимостей
poetry add django requests
poetry add "fastapi[standard]"

# Установка dev-зависимостей
poetry add --group dev pytest black

# Установка зависимостей из pyproject.toml
poetry install

# Активация окружения
poetry shell

# Запуск команд в окружении
poetry run python manage.py runserver

# Сборка пакета
poetry build

# Публикация в PyPI
poetry publish

# Удаляем пакет
poetry remove types-protobuf
```

```bash
[tool.poetry]
name = "myproject"
version = "0.1.0"
description = "My awesome project"
authors = ["Your Name <you@example.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.11"
django = "^4.1.0"
requests = "^2.28.0"
python-decouple = "^3.7"

[tool.poetry.group.dev.dependencies]
pytest = "^7.2.0"
black = "^23.1.0"
flake8 = "^6.0.0"
django-debug-toolbar = "^3.2.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"

[tool.black]
line-length = 88
target-version = ['py311']

[tool.isort]
profile = "black"
```

