#python 

Аннотация типов (Type Hints) — это способ указать ожидаемые типы аргументов функций, возвращаемых значений и переменных в Python. Они помогают разработчикам понимать код, IDE — предоставлять лучшую поддержку, а статическим анализаторам — находить ошибки.

#### 🔹 История и основы
Аннотации типов появились в Python 3.5 (PEP 484) и постоянно развиваются:
```python
# До Python 3.5 - комментарии
def greet(name):  # type: (str) -> str
    return f"Привет, {name}!"

# Python 3.5+ - аннотации типов
def greet(name: str) -> str:
    return f"Привет, {name}!"

# Python 3.8+ - более краткий синтаксис для литералов
from typing import List, Dict

# Старый синтаксис
def process_data(items: List[str], config: Dict[str, int]) -> None:
    pass

# Python 3.9+ - встроенные коллекции
def process_data(items: list[str], config: dict[str, int]) -> None:
    pass
```
---
#### 🔹 Базовые типы
```python
# Примитивные типы
age: int = 25
name: str = "Анна"
height: float = 1.75
is_student: bool = True

# Функции с аннотациями
def add(x: int, y: int) -> int:
    return x + y

def divide(x: float, y: float) -> float:
    if y == 0:
        raise ValueError("Деление на ноль")
    return x / y

def print_info(name: str, age: int) -> None:  # None для функций без возврата
    print(f"{name} ({age} лет)")

# Переменные с аннотациями (Python 3.6+)
count: int
message: str = "Сообщение по умолчанию"
```
---
#### 🔹 Коллекции и контейнеры
```python
from typing import List, Dict, Tuple, Set, Optional, Union

# Списки
numbers: list[int] = [1, 2, 3, 4, 5]
names: List[str] = ["Анна", "Иван", "Мария"]  # Старый синтаксис

# Словари
scores: dict[str, int] = {"Анна": 95, "Иван": 87}
config: Dict[str, Union[str, int]] = {"host": "localhost", "port": 8000}

# Кортежи
coordinates: tuple[float, float] = (10.5, 20.3)
rgb: Tuple[int, int, int] = (255, 128, 0)

# Кортежи переменной длины
scores: tuple[int, ...] = (95, 87, 92, 88)

# Множества
unique_ids: set[int] = {1, 2, 3, 4}
tags: Set[str] = {"python", "typing", "programming"}

# Функции с коллекциями
def calculate_average(numbers: list[float]) -> float:
    return sum(numbers) / len(numbers)

def get_user_info(users: dict[str, dict[str, Union[str, int]]]) -> str:
    """
    users: {"user1": {"name": "Анна", "age": 25}}
    """
    result = []
    for user_id, info in users.items():
        result.append(f"{info['name']} ({info['age']})")
    return ", ".join(result)
```
---
#### 🔹 Optional и Union
```python
from typing import Optional, Union

# Optional[T] эквивалентно Union[T, None]
def find_user(user_id: int) -> Optional[str]:
    users = {1: "Анна", 2: "Иван"}
    return users.get(user_id)  # Может вернуть None

# Union для нескольких возможных типов
def process_id(user_id: Union[int, str]) -> str:
    if isinstance(user_id, int):
        return f"ID: {user_id}"
    return f"Username: {user_id}"

# Python 3.10+ - новый синтаксис Union с |
def process_id_new(user_id: int | str) -> str:
    if isinstance(user_id, int):
        return f"ID: {user_id}"
    return f"Username: {user_id}"

# Значения по умолчанию
def create_user(name: str, age: int, email: Optional[str] = None) -> dict:
    user = {"name": name, "age": age}
    if email:
        user["email"] = email
    return user
```
---
#### 🔹 Callable и функции как параметры
```python
from typing import Callable, Any

# Функция как параметр
def apply_operation(x: int, y: int, operation: Callable[[int, int], int]) -> int:
    return operation(x, y)

def add(a: int, b: int) -> int:
    return a + b

def multiply(a: int, b: int) -> int:
    return a * b

# Использование
result1 = apply_operation(5, 3, add)       # 8
result2 = apply_operation(5, 3, multiply)  # 15

# Более сложные Callable
def process_data(
    data: list[str], 
    validator: Callable[[str], bool],
    transformer: Callable[[str], str]
) -> list[str]:
    return [transformer(item) for item in data if validator(item)]

# Функции без параметров или возврата
def run_task(task: Callable[[], None]) -> None:
    print("Запуск задачи...")
    task()
    print("Задача завершена")

# Callback функции
def fetch_data(url: str, callback: Callable[[dict], None]) -> None:
    # Имитация получения данных
    data = {"url": url, "status": "success"}
    callback(data)
```
---
#### 🔹 Generics (Обобщенные типы)
```python
from typing import TypeVar, Generic, List, Optional

# Создание типовой переменной
T = TypeVar('T')

# Обобщенная функция
def get_first_item(items: list[T]) -> Optional[T]:
    return items[0] if items else None

# Использование
first_number = get_first_item([1, 2, 3])      # int
first_name = get_first_item(["А", "Б", "В"])  # str

# Обобщенный класс
class Stack(Generic[T]):
    def __init__(self) -> None:
        self._items: list[T] = []
    
    def push(self, item: T) -> None:
        self._items.append(item)
    
    def pop(self) -> Optional[T]:
        return self._items.pop() if self._items else None
    
    def peek(self) -> Optional[T]:
        return self._items[-1] if self._items else None
    
    def is_empty(self) -> bool:
        return len(self._items) == 0

# Использование обобщенного класса
int_stack = Stack[int]()
int_stack.push(1)
int_stack.push(2)

str_stack = Stack[str]()
str_stack.push("hello")
str_stack.push("world")

# Ограничения типовых переменных
from typing import Protocol

class Comparable(Protocol):
    def __lt__(self, other: Any) -> bool: ...

CT = TypeVar('CT', bound=Comparable)

def sort_items(items: list[CT]) -> list[CT]:
    return sorted(items)
```
---
#### 🔹 Protocols и структурная типизация
```python
from typing import Protocol, runtime_checkable

# Определение протокола
class Drawable(Protocol):
    def draw(self) -> None: ...

class Movable(Protocol):
    def move(self, x: int, y: int) -> None: ...

# Классы, реализующие протоколы (неявно)
class Circle:
    def __init__(self, radius: float):
        self.radius = radius
    
    def draw(self) -> None:
        print(f"Рисуем круг радиуса {self.radius}")
    
    def move(self, x: int, y: int) -> None:
        print(f"Перемещаем круг в ({x}, {y})")

class Rectangle:
    def __init__(self, width: float, height: float):
        self.width = width
        self.height = height
    
    def draw(self) -> None:
        print(f"Рисуем прямоугольник {self.width}x{self.height}")

# Функции, работающие с протоколами
def render_shape(shape: Drawable) -> None:
    shape.draw()

def move_shape(shape: Movable, x: int, y: int) -> None:
    shape.move(x, y)

# Использование
circle = Circle(5.0)
rectangle = Rectangle(10.0, 20.0)

render_shape(circle)     # OK
render_shape(rectangle)  # OK
move_shape(circle, 10, 20)  # OK
# move_shape(rectangle, 10, 20)  # Ошибка типизации - Rectangle не Movable

# Runtime протоколы
@runtime_checkable
class Serializable(Protocol):
    def serialize(self) -> str: ...

class User:
    def __init__(self, name: str):
        self.name = name
    
    def serialize(self) -> str:
        return f"User(name='{self.name}')"

user = User("Анна")
if isinstance(user, Serializable):  # Проверка во время выполнения
    print(user.serialize())
```
---
#### 🔹 Literal и Final
```python
from typing import Literal, Final

# Literal - ограничение конкретными значениями
def set_log_level(level: Literal["DEBUG", "INFO", "WARNING", "ERROR"]) -> None:
    print(f"Установлен уровень логирования: {level}")

# Правильно
set_log_level("DEBUG")
# set_log_level("TRACE")  # Ошибка типизации

# Использование Literal с Union
Color = Literal["red", "green", "blue"]
Size = Literal["small", "medium", "large"]

def create_button(color: Color, size: Size) -> str:
    return f"Кнопка {color} размера {size}"

# Final - неизменяемые значения
MAX_CONNECTIONS: Final = 100
API_VERSION: Final[str] = "v1.0"

class Config:
    DATABASE_URL: Final[str] = "postgresql://localhost/mydb"
    DEBUG: Final[bool] = True

# Final для методов (предотвращает переопределение)
class BaseClass:
    @final  # Python 3.8+
    def important_method(self) -> str:
        return "Этот метод нельзя переопределить"
```
---
#### 🔹 Работа с классами
```python
from typing import ClassVar, Type, Self  # Self с Python 3.11

class Person:
    # Атрибут класса
    species: ClassVar[str] = "Homo sapiens"
    
    def __init__(self, name: str, age: int) -> None:
        self.name: str = name
        self.age: int = age
    
    def greet(self) -> str:
        return f"Привет, я {self.name}"
    
    @classmethod
    def from_string(cls: Type['Person'], data: str) -> 'Person':
        name, age_str = data.split(',')
        return cls(name.strip(), int(age_str.strip()))
    
    # С Python 3.11+ можно использовать Self
    def copy(self) -> 'Person':  # или Self
        return Person(self.name, self.age)

# Наследование с аннотациями
class Employee(Person):
    def __init__(self, name: str, age: int, salary: float) -> None:
        super().__init__(name, age)
        self.salary: float = salary
    
    def get_annual_salary(self) -> float:
        return self.salary * 12

# Фабричные функции
def create_person(person_type: Type[Person], name: str, age: int) -> Person:
    return person_type(name, age)

# Использование
person = create_person(Person, "Анна", 25)
employee = create_person(Employee, "Иван", 30)  # Ошибка типизации
```
---
#### 🔹 TypedDict для структурированных словарей
```python
from typing import TypedDict, NotRequired, Required

# Базовый TypedDict
class UserDict(TypedDict):
    name: str
    age: int
    email: str

# Использование
user: UserDict = {
    "name": "Анна",
    "age": 25,
    "email": "anna@example.com"
}

# TypedDict с необязательными полями (Python 3.11+)
class UserProfile(TypedDict):
    name: str
    age: int
    email: NotRequired[str]  # Необязательное поле
    phone: NotRequired[str]

# Или с total=False для всех полей
class PartialUser(TypedDict, total=False):
    name: str
    age: int
    email: str

# Наследование TypedDict
class ExtendedUser(UserDict):
    is_admin: bool
    last_login: str

def process_user(user: UserDict) -> str:
    return f"Пользователь {user['name']} ({user['age']} лет)"

# Функции для работы с TypedDict
def validate_user(user: UserDict) -> bool:
    return (
        isinstance(user.get('name'), str) and
        isinstance(user.get('age'), int) and
        isinstance(user.get('email'), str)
    )
```
---
#### 🔹 Инструменты для работы с типами
##### 🔸 mypy - статическая проверка типов
```python
# Установка
pip install mypy

# Проверка файла
mypy script.py

# Проверка пакета
mypy mypackage/
```

```python
# Конфигурация mypy
[mypy]
python_version = 3.11
warn_return_any = True
warn_unused_configs = True
disallow_untyped_defs = True
disallow_incomplete_defs = True
check_untyped_defs = True
disallow_untyped_decorators = True

# Игнорирование определенных модулей
[mypy-requests.*]
ignore_missing_imports = True

[mypy-some_package.*]
ignore_errors = True
```

##### 🔸 Примеры кода с аннотациями
```python
"""
Пример модуля с полными аннотациями типов
"""

from typing import Dict, List, Optional, Protocol, TypedDict, Union
from datetime import datetime
from dataclasses import dataclass
from abc import ABC, abstractmethod

# TypedDict для структурированных данных
class UserData(TypedDict):
    id: int
    username: str
    email: str
    created_at: str
    is_active: bool

# Dataclass с аннотациями
@dataclass
class User:
    id: int
    username: str
    email: str
    created_at: datetime
    is_active: bool = True
    
    def to_dict(self) -> UserData:
        return {
            "id": self.id,
            "username": self.username,
            "email": self.email,
            "created_at": self.created_at.isoformat(),
            "is_active": self.is_active
        }
    
    @classmethod
    def from_dict(cls, data: UserData) -> 'User':
        return cls(
            id=data["id"],
            username=data["username"],
            email=data["email"],
            created_at=datetime.fromisoformat(data["created_at"]),
            is_active=data["is_active"]
        )

# Протокол для хранилища
class UserStorage(Protocol):
    def save(self, user: User) -> None: ...
    def get(self, user_id: int) -> Optional[User]: ...
    def get_all(self) -> List[User]: ...
    def delete(self, user_id: int) -> bool: ...

# Конкретная реализация хранилища
class InMemoryUserStorage:
    def __init__(self) -> None:
        self._users: Dict[int, User] = {}
        self._next_id: int = 1
    
    def save(self, user: User) -> None:
        if user.id == 0:  # Новый пользователь
            user.id = self._next_id
            self._next_id += 1
        self._users[user.id] = user
    
    def get(self, user_id: int) -> Optional[User]:
        return self._users.get(user_id)
    
    def get_all(self) -> List[User]:
        return list(self._users.values())
    
    def delete(self, user_id: int) -> bool:
        if user_id in self._users:
            del self._users[user_id]
            return True
        return False

# Менеджер пользователей
class UserManager:
    def __init__(self, storage: UserStorage) -> None:
        self._storage = storage
    
    def create_user(
        self, 
        username: str, 
        email: str, 
        is_active: bool = True
    ) -> User:
        user = User(
            id=0,  # Будет установлен в storage.save()
            username=username,
            email=email,
            created_at=datetime.now(),
            is_active=is_active
        )
        self._storage.save(user)
        return user
    
    def get_user(self, user_id: int) -> Optional[User]:
        return self._storage.get(user_id)
    
    def get_active_users(self) -> List[User]:
        return [user for user in self._storage.get_all() if user.is_active]
    
    def update_user_status(self, user_id: int, is_active: bool) -> bool:
        user = self._storage.get(user_id)
        if user:
            user.is_active = is_active
            self._storage.save(user)
            return True
        return False
    
    def search_users(
        self, 
        query: str, 
        field: str = "username"
    ) -> List[User]:
        all_users = self._storage.get_all()
        if field == "username":
            return [u for u in all_users if query.lower() in u.username.lower()]
        elif field == "email":
            return [u for u in all_users if query.lower() in u.email.lower()]
        else:
            return []

# Функция для демонстрации
def main() -> None:
    # Создание компонентов
    storage: UserStorage = InMemoryUserStorage()
    manager = UserManager(storage)
    
    # Создание пользователей
    user1 = manager.create_user("anna123", "anna@example.com")
    user2 = manager.create_user("ivan456", "ivan@example.com")
    
    # Получение пользователей
    found_user = manager.get_user(user1.id)
    if found_user:
        print(f"Найден пользователь: {found_user.username}")
    
    # Поиск пользователей
    search_results = manager.search_users("anna", "username")
    print(f"Найдено пользователей: {len(search_results)}")

if __name__ == "__main__":
    main()
```
---
``