
- Лучшие практики для написния кода на fastapi  - https://github.com/zhanymkanov/fastapi-best-practices
- Awesome FastAPI - https://github.com/mjhea0/awesome-fastapi
- Фильтрация и пагинация в FastAPI - https://habr.com/ru/articles/714570/



пакет для тестирования
pip install pytest

пакет для форматирования кода
pip install black

применяемые веб инструменты
- FastAPI — сам веб-фреймворк, (pip install fastapi);
- Uvicorn — асинхронный веб-сервер, (pip install uvicorn);
- HTTPie — текстовый веб-клиент, похожий на curl, (pip install httpie);
- Requests — пакет синхронного веб-клиента, (pip install requests);
- HTTPX — пакет синхронного/асинхронного веб-клиента, (pip install httpx).
- Starlette - пакет используется для асинхронных операций
- Pydantic - библиотека  которая используется для валидации и структурирования данных с помощью подсказок типов
- mypy - статический анализатор типов, который помогает обнаруживать ошибки, связанные с несовпадением типов, ещё до запуска программы (pip install mypy).
- sqlalchemy - орм для баз данных (pip install sqlalchemy)


**Web Server Gateway Interface (WSGI)** (https://wsgi.readthedocs.io) — это спецификация стандарта синхронизации (https://peps.python.org/pep-3333) Python для подключения кода приложения к веб-серверам. Все традиционные веб-фреймворки Python построены на WSGI. Но синхронное взаимодействие может означать, что вы заняты ожиданием чего-то, что работает гораздо медленнее процессора, например запросов к дискам или сети. Тогда вы будете искать лучшую конкурентность. В последние годы она приобретает все большее значение. В результате была разработана спецификация **Asynchronous Server Gateway Interface (ASGI)** (https://asgi.readthedocs.io) для Python. 

🔹 Установить FastAPI
```bash
pip install "fastapi[standard]"
```

🔹 Запуск FastAPI
```bash
# быстрый запуск для разработки
$ fastapi dev main.py

# Запуская Uvicorn напрямую
# Слово hello дает ссылку на файл hello.py, а слово app — это имя переменной
$ uvicorn hello:app --reload

# Запуска через приложени, используется на продакте
# в главном (main.py) файле прописываем
if __name__ == '__main__':
	uvicorn.run('main.app')

# запускаем приложение стандартным образом
$ python main.py

# если приложение нужно запустить в фоне
$ python main.py &
```

```bash
# увидеть запущен ли сервер uvicorn
$ ps aux | grep uvicorn

# если нужно остановить сервре, то убиваем процесс, берем PID из предыдущей команды и выполняем команду

$ kill -9 <PID>
```

🔹 По пути http://127.0.0.1:8000/docs будет генерироваться (автоматически) api-документация (OpenAPI)

---

##### Типы конкурентности

* При ==параллельных== вычислениях задача распределяется между несколькими выделенными центральными процессорами (ЦП). Этот метод часто используется в приложениях для выполнения расчетов, таких как задачи обработки графики и машинное обучение.
* При ==конкурентных== вычислениях каждый ЦП переключается между несколькими задачами. Некоторые задачи из потока занимают больше времени, и необходимо сократить общее время выполнения. Считывание файла или доступк удаленному сетевому сервису буквально в тысячи и миллионы раз медленнее, чем выполнение вычислений в ЦП


Асинхронность
FastAPI сам вызывает асинхронную функцию, когда получает GET-запрос. Вам не нужно добавлять ключевое слово await куда-либо.Но для любых других определений функций async def вызывающая сторона должна поместить оператор await перед каждым вызовом

---

БД в оперативной памяти

`aiosqlite` — это ==асинхронная библиотека для работы с базой данных SQLite в языке программирования Python==. Она позволяет выполнять запросы к базе данных SQLite в неблокирующем режиме, что очень важно для асинхронного кода, такого как боты или веб-приложения, где необходимо одновременно обрабатывать несколько запросов без задержек

```bash
pip install aiosqlite
```

---
#### 🔹 START PROJECT
```bash
mkdir app/src && cd app

python3 -m venv venv && source venv/bin/activate

pip install "fastapi[standard]"
pip install python-dotenv
pip install pydantic-settings
pip install SQLAlchemy

touch {.env,.env.dist,.gitignore}

cd src && touch {main,__init__,config,database}.py

mkdir requirements \
	&& cd requirements \
	&& touch {base,dev,test,prod}.txt \
	&& cd ../

mkdir user && cd user && touch {router,schemas,models,dependencies,config,constans,exceptions,service,utils}.py
```

🔸 **src/main.py**
```python
from fastapi import FastAPI  
import uvicorn  
  
app = FastAPI()  
  
@app.get("/")  
async def root():  
    return {  
        "app": "API",  
        "env": 'local',  
        "version": 1.0  
    }  
  
if __name__ == "__main__":  
    uvicorn.run("main:app", reload=True)
```

🔸 **запускаем сервер**
```bash
 python3 src/main.py
```
---
#### 🔹 Подключаем бд

##### 🔸 Postgres

устанавливаем такие пакеты
- **psycopg2-binary** — драйвер для PostgreSQL
- **Alembic** — инструмент для управления миграциями
```bash
pip install sqlalchemy psycopg2-binary alembic
```

- Создаем файл с конфигом или добавляем в существующий
```python
from pydantic_settings import BaseSettings, SettingsConfigDict  
  
class DatabaseConfig(BaseSettings):  
    model_config = SettingsConfigDict(  
        env_file=".env",  
        env_prefix="DB_", 
        extra="ignore"  
    )  
     # значение по умолчанию  
    host: str = "localhost"  
    port: int = 5432  
    username: str  
    password: str  
    database: str  
  
    def url(self) -> str:  
        return f"postgresql+psycopg2://{self.username}:{self.password}@{self.host}:{self.port}/{self.database}"  
  
class Config(BaseSettings):  
    db: DatabaseConfig = DatabaseConfig()
```

- в .env добавляем переменные для подключения
```env
DB_HOST=localhost  
DB_DATABASE=db  
DB_PORT=5432  
DB_USERNAME=root  
DB_PASSWORD=password
```

- создаем файл -`database.py` (где будет сессия подключения к бд)
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base
from src.config import Config

config = Config()

engine = create_engine(
    config.db.url,
    echo=True,
    pool_pre_ping=True,
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

-  Инициализация Alembic и настраиваем конфигурацию Alembic (`alembic/env.py`), более подробно про Alembic здесь - [[alembic]]

- Создаем модели 
```python
from sqlalchemy import Column, Integer, String, DateTime  
from datetime import datetime  
from src.database import Base  
  
class User(Base):  
    __tablename__ = "users"  
    id = Column(Integer, primary_key=True, index=True)  
    name = Column(String(255), nullable=False)  
    email = Column(String(255), unique=True, nullable=False)  
    created_at = Column(DateTime, default=datetime.utcnow)
```

- Создание первой миграции
```bash
alembic revision --autogenerate -m "Initial migration"
```

- Применение миграций
```bash
alembic upgrade head
```

---
#### 🔹 STRUCTURA PROJECT

Это паттерн, часто называемый ==**"Feature-based structure"**== — всё, что связано с одной сущностью, хранится в одной папке
```bash
fastapi-project
├── alembic/
├── src
│   ├── auth
│   │   ├── router.py # Эндпоинты (роуты)
│   │   ├── schemas.py  # Pydantic модели для запросов/ответов
│   │   ├── models.py  # ORM модели (SQLAlchemy и т.п.)
│   │   ├── dependencies.py # Зависимости FastAPI
│   │   ├── config.py  # local configs
│   │   ├── constants.py # Константы модуля
│   │   ├── exceptions.py # Кастомные исключения
│   │   ├── service.py # Бизнес-логика
│   │   └── utils.py # Вспомогательные функции
│   ├── aws
│   │   ├── client.py  # client model for external service communication
│   │   ├── schemas.py
│   │   ├── config.py
│   │   ├── constants.py
│   │   ├── exceptions.py
│   │   └── utils.py
│   └── posts
│   │   ├── router.py
│   │   ├── schemas.py
│   │   ├── models.py
│   │   ├── dependencies.py
│   │   ├── constants.py
│   │   ├── exceptions.py
│   │   ├── service.py
│   │   └── utils.py
│   ├── config.py  # global configs
│   ├── models.py  # global models
│   ├── exceptions.py  # global exceptions
│   ├── pagination.py  # global module e.g. pagination
│   ├── database.py  # db connection related stuff
│   └── main.py
├── tests/
│   ├── auth
│   ├── aws
│   └── posts
├── templates/
│   └── index.html
├── requirements
│   ├── base.txt
│   ├── dev.txt
│   └── prod.txt
├── .env
├── .gitignore
├── logging.ini
└── alembic.ini
```

---



