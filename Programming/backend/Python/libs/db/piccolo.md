#python #database #package #orm #async

- https://github.com/piccolo-orm/piccolo

#### 🔹 Основные преимущества Piccolo:

- **Асинхронность** — поддержка async/await  
- **Type hints** — полная поддержка типизации  
- **Простота** — интуитивный API  
-  **ORM** — объектно-ориентированное взаимодействие с БД  
-  **Миграции** — встроенная система управления схемой  
-  **Admin панель** — готовый интерфейс администратора  
-  **Кроссплатформенность** — работает с SQLite, PostgreSQL, MySQL

```bash
# install
pip install piccolo
poetry add "piccolo[sqlite]"
```

``` python
from piccolo.columns import Varchar, Integer, Boolean, Date, Timestamp
from piccolo.engine.sqlite import SQLiteEngine
from piccolo.table import Table

# Создание подключения к БД
DB = SQLiteEngine("database.db")
```

#### 🔹 Определение моделей таблиц
``` python
from piccolo.columns import Varchar, Integer, Boolean, Date, ForeignKey
from piccolo.table import Table

class User(Table, db=DB):
    # Основные типы полей
    id = Integer(primary_key=True)  # Автоинкремент
    username = Varchar(length=100, unique=True)
    email = Varchar()
    age = Integer()
    is_active = Boolean(default=True)
    created_at = Timestamp()
    bio = Varchar(null=True)  # Опциональное поле

class BlogPost(Table, db=DB):
    id = Integer(primary_key=True)
    title = Varchar()
    content = Varchar()
    author = ForeignKey(references=User)  # Связь с User
```

#### 🔹 Доступные типы данных
``` python
from piccolo.columns import (
    Varchar,        # Строки
    Integer,        # Целые числа
    SmallInt,       # Маленькие целые
    BigInt,         # Большие целые
    Numeric,        # Числа с плавающей точкой
    Boolean,        # Логические значения
    Date,           # Даты
    Timestamp,      # Временные метки
    Interval,       # Интервалы времени
    JSON,           # JSON данные
    JSONB,          # JSON (оптимизированный)
    Array,          # Массивы
    UUID,           # UUID
    Secret,         # Секретные данные
    Text,           # Большой текст
)
```

#### 🔹 Миграции
```bash
# Создание новой миграции
piccolo migration create migration_name

# Применение миграций
piccolo migration run

# Просмотр статуса миграций
piccolo migration status
```

#### 🔹 Базовые операции с данными (async/await)
``` python
import asyncio
from datetime import datetime

async def examples():
    # CREATE - Создание записи
    user = User(username="john_doe", email="john@example.com", age=25)
    await user.save()
    
    # Создание нескольких записей
    users = [
        User(username="alice", email="alice@example.com", age=30),
        User(username="bob", email="bob@example.com", age=28),
    ]
    await User.insert(*users).run()
    
    # READ - Чтение данных
    all_users = await User.select().run()
    
    # Получение одной записи
    user = await User.select().where(
        User.username == "john_doe"
    ).first()
    
    # Фильтрация
    adults = await User.select().where(
        User.age >= 18
    ).run()
    
    # SELECT определенных полей
    names = await User.select(User.username, User.email).run()
    
    # UPDATE - Обновление данных
    await User.update({User.age: 26}).where(
        User.username == "john_doe"
    ).run()
    
    # DELETE - Удаление данных
    await User.delete().where(
        User.username == "bob"
    ).run()

# Запуск асинхронного кода
asyncio.run(examples())
```

#### 🔹 Продвинутые запросы
``` python
async def advanced_queries():
    # Сортировка
    sorted_users = await User.select().order_by(
        User.age, ascending=False
    ).run()
    
    # Лимит и смещение
    page = await User.select().limit(10).offset(20).run()
    
    # Группировка и агрегация
    from piccolo.query.functions import Count, Sum, Avg, Max, Min
    
    count = await User.select(
        Count(User.id)
    ).run()
    
    # JOIN с другой таблицей
    posts_with_authors = await BlogPost.select(
        BlogPost.title,
        User.username
    ).join(User).run()
    
    # Сложные условия
    from piccolo.query.functions import Or, And
    
    results = await User.select().where(
        (User.age > 25) & (User.is_active == True)
    ).run()
    
    # OR условие
    results = await User.select().where(
        (User.username == "john") | (User.username == "alice")
    ).run()
```

#### 🔹 Валидация и проверк
```python
class User(Table, db=DB):
    username = Varchar(
        length=50,
        unique=True,
        index=True  # Индекс для быстрого поиска
    )
    email = Varchar(required=True)
    age = Integer(
        ge=0,      # greater or equal
        le=150     # less or equal
    )
```

#### 🔹 Работа с Piccolo Admin
``` bash
# Запуск админ-панели
piccolo admin create

# Запуск админ-интерфейса
piccolo admin run
```

#### 🔹 Hooks
``` python
from piccolo.table import Table
from datetime import datetime

class Article(Table, db=DB):
    title = Varchar()
    created_at = Timestamp()
    
    async def before_insert(self):
        """Вызывается перед добавлением записи"""
        self.created_at = datetime.now()
    
    async def after_save(self):
        """Вызывается после сохранения"""
        print(f"Статья '{self.title}' была сохранена")
```

#### 🔹 Использование с Starlette/FastAPI
``` python
from starlette.applications import Starlette
from starlette.responses import JSONResponse

app = Starlette()

@app.route('/users', methods=['GET'])
async def get_users(request):
    users = await User.select().run()
    return JSONResponse(users)

@app.route('/users', methods=['POST'])
async def create_user(request):
    data = await request.json()
    user = User(**data)
    await user.save()
    return JSONResponse({"id": user.id}, status_code=201)
```