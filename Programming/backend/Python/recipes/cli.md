#python #package #terminal #cli #recipes

#### 🔹 Создание красивых и удобных CLI-приложений
 Используем пакета **Typer** и **Rich**

```bash
pip install typer rich
```

==Простой пример==
```python
# main.py
import typer

def main(name: str):
    """
    Говорит "Привет" пользователю.
    """
    print(f"Привет, {name}!")

if __name__ == "__main__":
    typer.run(main)
```

```bash
# информация о использовании команды
python main.py --help

# запуск
python main.py max
```

##### 🔸 Аргументы, опции и типы данных
CLI-приложения состоят из аргументов (обычно обязательных) и опций (необязательных, часто начинаются с `--`). Typer элегантно управляется и с теми, и с другими.
```python
import typer

def main(
    name: str,
    lastname: str = typer.Option("", help="Фамилия пользователя."),
    formal: bool = typer.Option(False, "--formal", "-f", help="Использовать формальное приветствие."),
):
    """
    Говорит "Привет" пользователю, опционально используя фамилию и формальный стиль.
    """
    if formal:
        print(f"Добрый день, {name} {lastname}!")
    else:
        print(f"Привет, {name}!")

if __name__ == "__main__":
    typer.run(main)
```

##### 🔸 Основы: цвета и стили
```python
from rich import print

print("Это обычный текст.")
print("[bold green]Это жирный зеленый текст![/bold green]")
print("[italic yellow]А это желтый курсив.[/italic yellow]")
print("[underline cyan]Можно даже так.[/underline cyan]")
print("[bold red on white]Красный на белом фоне![/bold red on white]")
```

##### 🔸 Структурированный вывод: таблицы
```python
from rich import print
from rich.table import Table

# 1. Создаем объект таблицы
table = Table(title="Список моих любимых фреймворков")

# 2. Добавляем колонки
table.add_column("Название", justify="left", style="cyan", no_wrap=True)
table.add_column("Язык", style="magenta")
table.add_column("Для чего", justify="right", style="green")

# 3. Наполняем данными
table.add_row("FastAPI", "Python", "Веб-API")
table.add_row("React", "JavaScript", "Фронтенд")
table.add_row("Typer", "Python", "CLI-приложения")

# 4. Выводим таблицу в консоль
print(table)
```

##### 🔸 Интерактивность: прогресс-бары
```python
import time
from rich.progress import track

for step in track(range(10), description="Обработка данных..."):
    # Симулируем какую-то работу
    time.sleep(0.5)

print("[bold green]Готово![/bold green]")
```

##### 🔸  утилита для проверки доступности сайтов
```bash
pip install typer rich requests
```

```python
# site_checker.py
import typer
import requests
from typing import List
from rich.console import Console
from rich.table import Table
from rich.progress import track

console = Console()

def get_status_emoji(status_code: int) -> str:
    """Возвращает эмодзи в зависимости от статус-кода."""
    if 200 <= status_code < 300:
        return "✅ OK"
    elif 300 <= status_code < 400:
        return "➡️ REDIRECT"
    elif 400 <= status_code < 500:
        return "❌ CLIENT ERROR"
    elif 500 <= status_code < 600:
        return "🔥 SERVER ERROR"
    return "❓ UNKNOWN"

def main(urls: List[str] = typer.Argument(..., help="Список URL для проверки.")):
    """
    Проверяет доступность сайтов и выводит результат в виде таблицы.
    """
    table = Table(title="Результаты проверки сайтов")
    table.add_column("URL", style="cyan", no_wrap=True)
    table.add_column("Статус код", justify="center")
    table.add_column("Статус", justify="left", style="green")

    for url in track(urls, description="Проверка сайтов..."):
        try:
            response = requests.get(url, timeout=5)
            status_code = response.status_code
            status_text = get_status_emoji(status_code)
            
            # Раскрашиваем строку в зависимости от статуса
            row_style = ""
            if 300 <= status_code < 400:
                row_style = "yellow"
            elif status_code >= 400:
                row_style = "red"
            
            table.add_row(url, str(status_code), status_text, style=row_style)

        except requests.exceptions.RequestException as e:
            table.add_row(url, "N/A", f"💥 ERROR: {e.__class__.__name__}", style="bold red")

    console.print(table)

if __name__ == "__main__":
    typer.run(main)
```

```bash
python site_checker.py https://goodsite.io https://py-tools.com https://this-is-a-fake-domain.net
```
---