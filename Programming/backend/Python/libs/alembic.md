#python #tools #package #database #migration

Docs - https://alembic.sqlalchemy.org/en/latest/

**Alembic** — это инструмент управления версиями миграций БД для **SQLAlchemy**. Он позволяет:
- Отслеживать изменения схемы БД
- Откатывать и переприменять миграции
- Работать в команде без конфликтов
- Контролировать версии БД

#### 🔹 Установка и инициализация

```bash
pip install alembic
alembic init alembic
```

==После этого создаются:==
- `alembic/` — директория с миграциями
- `alembic/versions/` — папка с файлами миграций
- `alembic.ini` — конфиг Alembic
- `alembic/env.py` — окружение для выполнения миграций `alembic/env.py`
---
#### 🔹 Основные команды
##### 🔸 Создание миграций
```bash
# Автоматическая генерация на основе моделей SQLAlchemy
alembic revision --autogenerate -m "Add users table"

# Создание пустой миграции (для ручного написания)
alembic revision -m "Custom migration"
```

##### 🔸 Применение и откат
```bash
# Применить все неприменённые миграции
alembic upgrade head

# Применить N миграций
alembic upgrade +2

# Откатить на N версий назад
alembic downgrade -1
alembic downgrade -2

# Откатить все миграции
alembic downgrade base

# Откатить на конкретную версию
alembic downgrade abc123def456
```

##### 🔸 Просмотр статуса
```bash
# Текущая версия БД
alembic current

# История всех миграций
alembic history

# История с описанием
alembic history --verbose

# Только применённые миграции
alembic history -r base:current

# Миграции, которые еще не применены
alembic history -r current:heads
```

##### 🔸 Слияние веток (важно для команды!)
```bash
# Если два разработчика создали миграции с одной базой
alembic merge -m "merge branches"
```
---
#### 🔹 Структура файла миграции

```python
"""Add users table

Revision ID: 001_add_users
Revises: 
Create Date: 2025-01-10 10:30:00.000000

"""
from alembic import op
import sqlalchemy as sa

# Идентификатор ревизии
revision = '001_add_users'
# На какую миграцию ссылается
down_revision = None
# Для слияния веток
branch_labels = None
conflicts_tags = None

def upgrade() -> None:
    """Применить изменения"""
    pass

def downgrade() -> None:
    """Откатить изменения"""
    pass
```
---
#### 🔹 Создание таблиц БЕЗ Pydantic

##### 🔸 Полностью ручная миграция
```bash
alembic revision -m "Create users table manually"
```

==Затем отредактируйте созданный файл:==
```python
def upgrade() -> None:
    op.create_table(
        'users',
        sa.Column('id', sa.Integer, primary_key=True),
        sa.Column('username', sa.String(50), unique=True, nullable=False),
        sa.Column('email', sa.String(120), unique=True, nullable=False),
        sa.Column('created_at', sa.DateTime, server_default=sa.func.now()),
    )
    op.create_index('idx_username', 'users', ['username'])

def downgrade() -> None:
    op.drop_index('idx_username', 'users')
    op.drop_table('users')
```

##### 🔸 Операции над таблицами
```python
def upgrade() -> None:
    # Создание таблицы
    op.create_table(
        'posts',
        sa.Column('id', sa.Integer, primary_key=True),
        sa.Column('title', sa.String(200), nullable=False),
        sa.Column('content', sa.Text),
        sa.Column('user_id', sa.Integer, sa.ForeignKey('users.id'), nullable=False),
    )
    
    # Добавление колонки
    op.add_column('users', sa.Column('bio', sa.Text))
    
    # Изменение колонки (тип, nullable и т.д.)
    op.alter_column('users', 'email', 
                    existing_type=sa.String(120),
                    type_=sa.String(255),
                    nullable=False)
    
    # Удаление колонки
    op.drop_column('users', 'temporary_field')
    
    # Переименование колонки
    op.alter_column('users', 'bio', new_column_name='biography')
    
    # Создание индекса
    op.create_index('idx_user_email', 'users', ['email'])
    
    # Создание уникального ограничения
    op.create_unique_constraint('uq_users_email', 'users', ['email'])
    
    # Добавление check constraint
    op.create_check_constraint('ck_age_positive', 'users', 'age > 0')

def downgrade() -> None:
    op.drop_constraint('ck_age_positive', 'users')
    op.drop_constraint('uq_users_email', 'users')
    op.drop_index('idx_user_email', 'users')
    op.alter_column('users', 'biography', new_column_name='bio')
    op.add_column('users', sa.Column('temporary_field', sa.String))
    op.drop_column('users', 'bio')
    op.alter_column('users', 'email', 
                    existing_type=sa.String(255),
                    type_=sa.String(120))
    op.drop_column('users', 'title')
    op.drop_table('posts')
```

##### 🔸 Вставка данных
```python
def upgrade() -> None:
    op.execute(
        sa.text(
            "INSERT INTO users (username, email) VALUES (:username, :email)"
        ),
        [
            {"username": "admin", "email": "admin@example.com"},
            {"username": "user", "email": "user@example.com"},
        ]
    )

def downgrade() -> None:
    op.execute(sa.text("DELETE FROM users WHERE username IN ('admin', 'user')"))
```

##### 🔸 Выполнение SQL запросов
```python
def upgrade() -> None:
    # Просто SQL
    op.execute("""
        CREATE TABLE IF NOT EXISTS logs (
            id SERIAL PRIMARY KEY,
            message TEXT,
            created_at TIMESTAMP DEFAULT NOW()
        )
    """)
    
    # С параметрами (безопаснее!)
    op.execute(
        sa.text("UPDATE users SET role = :role WHERE id = :id"),
        [{"role": "admin", "id": 1}]
    )

def downgrade() -> None:
    op.execute("DROP TABLE IF EXISTS logs")
```
---
#### 🔹 Комбинированный подход (Pydantic + Alembic)

##### 🔸 Определите модель в `src/models.py`
```python
from src.database import Base
from sqlalchemy import Column, Integer, String, DateTime
from datetime import datetime

class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True)
    username = Column(String(50), unique=True, nullable=False)
    email = Column(String(120), unique=True, nullable=False)
    created_at = Column(DateTime, default=datetime.utcnow)
```

##### 🔸 Сгенерируйте автоматическую миграцию
```bash
alembic revision --autogenerate -m "Add users table"
```

##### 🔸 Отредактируйте файл миграции, если нужно
```python
def upgrade() -> None:
    op.create_table(
        'users',
        sa.Column('id', sa.Integer(), nullable=False),
        sa.Column('username', sa.String(length=50), nullable=False),
        sa.Column('email', sa.String(length=120), nullable=False),
        sa.Column('created_at', sa.DateTime(), nullable=True),
        sa.PrimaryKeyConstraint('id'),
        sa.UniqueConstraint('email'),
        sa.UniqueConstraint('username'),
    )
    op.create_index('idx_user_email', 'users', ['email'])

def downgrade() -> None:
    op.drop_index('idx_user_email', 'users')
    op.drop_table('users')
```

> ⚠️ Alembic сканирует все модели, которые наследуются от `Base.metadata`. Если модели используют разные `Base` классы — Alembic их не видит! 

---
#### 🔹 Конфигурация `alembic.ini`

```ini
# Путь к скриптам миграций
script_location = alembic

# Расширения версий (для поддержки ветвления)
version_path_separator = :

# SQLAlchemy database URL
sqlalchemy.url = driver://user:password@localhost/dbname

# Логирование
[loggers]
keys = sqlalchemy.engine,sqlalchemy.pool,alembic

[handlers]
keys = console

[formatters]
keys = generic

[logger_root]
level = WARN
handlers = console
qualname =

[logger_sqlalchemy.engine]
level = WARN
handlers =
qualname = sqlalchemy.engine

[logger_sqlalchemy.pool]
level = WARN
handlers =
qualname = sqlalchemy.pool

[logger_alembic]
level = INFO
handlers =
qualname = alembic

[handler_console]
class = StreamHandler
args = (sys.stderr,)
level = NOTSET
formatter = generic

[formatter_generic]
format = %(levelname)-5.5s [%(name)s] %(message)s
datefmt = %H:%M:%S
```
---
#### 🔹 Правильная конфигурация `alembic/env.py`

```python
from logging.config import fileConfig
from sqlalchemy import engine_from_config, pool, MetaData
from alembic import context
import os
import sys

# Добавьте путь к вашему проекту
sys.path.insert(0, os.path.dirname(os.path.dirname(__file__)))

from src.database import Base
from src.config import Config

config = context.config
config.set_main_option("sqlalchemy.url", Config().db.url)

if config.config_file_name is not None:
    fileConfig(config.config_file_name)

target_metadata = Base.metadata

def run_migrations_offline() -> None:
    """Режим без подключения к БД (для CI/CD)"""
    url = config.get_main_option("sqlalchemy.url")
    context.configure(
        url=url,
        target_metadata=target_metadata,
        literal_binds=True,
        dialect_opts={"paramstyle": "named"},
    )
    
    with context.begin_transaction():
        context.run_migrations()

def run_migrations_online() -> None:
    """Режим с подключением к БД"""
    connectable = engine_from_config(
        config.get_section(config.config_loader_name),
        prefix="sqlalchemy.",
        poolclass=pool.NullPool,
    )
    
    with connectable.connect() as connection:
        context.configure(
            connection=connection,
            target_metadata=target_metadata
        )
        
        with context.begin_transaction():
            context.run_migrations()

if context.is_offline_mode():
    run_migrations_offline()
else:
    run_migrations_online()
```
---
#### 🔹 Работа в команде

```bash
# Каждый разработчик создаёт свою миграцию
git checkout -b feature/add-roles
alembic revision --autogenerate -m "Add roles table"
# ... правим миграцию ...
git add alembic/versions/xxx_add_roles.py
git push

# Когда две ветки мержат
git merge feature/add-roles

# Если обе имеют миграции с одной базой — конфликт!
# Решение: создайте миграцию слияния
alembic merge -m "merge branches"

# История будет выглядеть так:
# base -> migration_1 -> migration_2_branch_a -> merge_migration
#              \     -> migration_2_branch_b ---^
```
---