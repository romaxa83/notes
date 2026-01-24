#python 

#### 🔹 Создание класса и объекта

Первый аргумент каждого метода всегда обозначает сам объект. По общепринятому
соглашению этому аргументу присваивается имя self. Все операции, где задействованы атрибуты объекта, должны явно ссылаться на переменную self.
Методы, имена которых начинаются и заканчиваются двумя символами подчеркивания, выполняют специальные операции. Например, метод `__init__` используется для инициализации объекта.
В Python нет механизмов сокрытия или защиты данных. Но у программистов есть соглашение: имена, начинающиеся с одного символа подчеркивания, считаются «приватными».

```python
class Person:
    # Атрибут класса (общий для всех экземпляров)
    species = "Homo sapiens"
    
    # Конструктор класса
    def __init__(self, name, age):
        # Атрибуты экземпляра
        self.name = name
        self.age = age
    
    # Метод экземпляра
    def introduce(self):
        return f"Привет, меня зовут {self.name}, мне {self.age} лет"
    
    # Метод экземпляра
    def have_birthday(self):
        self.age += 1
        return f"У {self.name} день рождения! Теперь {self.age} лет"
        
# Создание объектов (экземпляров класса)
person1 = Person("Анна", 25)
person2 = Person("Иван", 30)

# Использование методов
print(person1.introduce())  # Привет, меня зовут Анна, мне 25 лет
print(person2.introduce())  # Привет, меня зовут Иван, мне 30 лет

# Доступ к атрибутам
print(person1.name)     # Анна
print(person1.species)  # Homo sapiens

# Вызов методов
print(person1.have_birthday())  # У Анна день рождения! Теперь 26 лет

# позволяет просматривать переменные обьекта 
vars(person1)
# или можно просматривать так
person1.__dict__

# получаем тип обьекта
type(person1)

# манипулировать атрибутами можно и через встроенные функции
# получаем атрибут
getattr(person1, 'age')
# устанавливаем атрибут 
setattr(person1, 'age', 44)
# удаляем атрибут
delattr(person1, 'age')
# проверяем наличие атрибута
hasattr(person1, 'age')

# проверки принадлежности к классу,  Она возвращает True, если объект obj принадлежит cls или любому классу, производному от cls
isinstance(person1, Person) # True
```

> Если нужно получить имя класса в методе - `type(self).__name__`

##### 🔸 `__str__`
Чтобы задать осмысленное текстовое представление объекта, в классе используют специальный метод `__str__`.Таким образом, метод `__str__` позволяет один раз описать, как объект должен выглядеть в тексте, и после этого Python будет использовать это описание автоматически — в `print`, логах и других похожих ситуациях.

```python
class Character:
    def __init__(self, name, health):
        self.name = name
        self.health = health

    def __str__(self):
        return f"{self.name} (здоровье: {self.health})"

hero = Character("Archer", 100)
print (hero) # Archer (здоровье: 100)
```

Иногда рядом с `__str__` можно встретить метод `__repr__`. Он тоже возвращает строку, но используется в других ситуациях.
`__str__` отвечает за то, как объект выглядит при обычном выводе — например, когда мы используем `print`.
`__repr__` используется, когда Python показывает объект в служебных контекстах: в отладке, в интерактивной консоли или внутри коллекций.
Если метод `__str__` не определён, Python использует `__repr__` вместо него.
На практике `__str__` делают коротким и удобным для чтения, а `__repr__` — более точным описанием объекта.

##### 🔸  `__len__`
В Python есть встроенная функция `len()`, которая возвращает длину строки, списка или словаря. Она может работать и с объектами пользовательских классов, если мы явно зададим это поведение.
По умолчанию Python не знает, что считать длиной такого объекта, поэтому для этого в классе определяют специальный метод `__len__`
```python
class Character:
    def __init__(self, name, items):
        self.name = name
        self.items = items

    def __len__(self):
        return len(self.items)

hero = Character("Archer", ["лук", "стрелы", "зелье"])
print(len(hero))  # 3
```

##### 🔸 `__eq__`
Иногда нам нужно понять, равны ли два объекта. Например, представим, что у двух персонажей в игре одинаковые характеристики, и мы хотим это проверить.
Если сравнить их напрямую через `hero1 == hero2`, Python по умолчанию считает объекты разными, даже если данные внутри одинаковые. Он сравнивает не содержимое, а то, являются ли это одни и те же объекты в памяти.
Чтобы объяснить Python, по каким признакам объекты считаются равными, в классе определяют специальный метод `__eq__`
```python
class Character:
    def __init__(self, name, health):
        self.name = name
        self.health = health

    def __eq__(self, other):
        return (
            self.name == other.name and
            self.health == other.health
        )

hero1 = Character("Archer", 100)
hero2 = Character("Archer", 100)

print(hero1 == hero2) # True
```

##### 🔸 Другие методы

- `__iter__` — позволяет проходить по объекту в цикле `for`;
- `__getitem__` — отвечает за доступ к элементам по индексу (`obj[i]`);
- `__enter__` и `__exit__` — позволяют объекту работать с конструкцией `with` (например, как файл);
- `__add__`, `__sub__`, `__mul__` — переопределяют арифметические операции между объектами.
---
#### 🔹 Классы данных

Бывает, нам нужен класс, который просто хранит данные — без сложной логики и десятков методов.

в Python добавили **декоратор** `@dataclass` — он создаёт стандартные методы автоматически

Тут подробнее [[Dataclass]]

---
#### 🔹 Основные концепции ООП

##### 🔸 Инкапсуляция
>Сокрытие внутренней реализации и предоставление контролируемого доступа к данным.
  В языке Python все атрибуты и методы классов открыты — доступны без ограничений. Использует соглашения об именах как средство обозначения предполагаемого использования.

```python
class BankAccount:
    def __init__(self, initial_balance=0):
        self._balance = initial_balance  # Защищенный атрибут
        self.__account_number = "12345"  # Приватный атрибут
    
    def deposit(self, amount):
        if amount > 0:
            self._balance += amount
            return f"Внесено {amount}. Баланс: {self._balance}"
        return "Сумма должна быть положительной"
    
    def get_balance(self):
        return self._balance
    
    def _internal_method(self):  # Защищенный метод
        return "Внутренний метод"
    
    def __private_method(self):  # Приватный метод
        return "Приватный метод"

account = BankAccount(1000)
print(account.deposit(500))      # Внесено 500. Баланс: 1500
print(account.get_balance())     # 1500
```


##### 🔸 Наследование
Создание новых классов на основе существующих
```python
class Animal:
    def __init__(self, name, species):
        self.name = name
        self.species = species
    
    def make_sound(self):
        return f"{self.name} издает звук"
    
    def info(self):
        return f"{self.name} - {self.species}"

class Dog(Animal):  # Dog наследуется от Animal
    def __init__(self, name, breed):
        super().__init__(name, "Собака")  # Вызов конструктора родителя
        self.breed = breed
    
    def make_sound(self):  # Переопределение метода
        return f"{self.name} лает: Гав-гав!"
    
    def fetch(self):  # Новый метод
        return f"{self.name} приносит мячик"

class Cat(Animal):
    def __init__(self, name, color):
        super().__init__(name, "Кошка")
        self.color = color
    
    def make_sound(self):
        return f"{self.name} мяукает: Мяу!"

# Использование
dog = Dog("Рекс", "Лабрадор")
cat = Cat("Мурка", "Рыжая")

print(dog.info())        # Рекс - Собака
print(dog.make_sound())  # Рекс лает: Гав-гав!
print(dog.fetch())       # Рекс приносит мячик

print(cat.make_sound())  # Мурка мяукает: Мяу!
```

> Наследоваться можно от нескольких классов
```python
class HotDog(Sandwich, Taco):
	pass
```

⚠️ ==Если есть возможность лучше избегать наследование а вместо нее использовать композицию==
##### 🔸 Полиморфизм
Возможность использовать объекты разных классов через общий интерфейс
```python
def animal_concert(animals):
    for animal in animals:
        print(animal.make_sound())

# Список животных разных типов
pets = [
    Dog("Барон", "Овчарка"),
    Cat("Василий", "Серый"),
    Dog("Лайка", "Хаски")
]

animal_concert(pets)
# Барон лает: Гав-гав!
# Василий мяукает: Мяу!
# Лайка лает: Гав-гав!
```
---
#### 🔹 Специальные методы (магические методы)
```python
class Book:
    def __init__(self, title, author, pages):
        self.title = title
        self.author = author
        self.pages = pages
    
    def __str__(self):  # Строковое представление для пользователя
        return f"{self.title} - {self.author}"
    
    def __repr__(self):  # Строковое представление для разработчика
        return f"Book('{self.title}', '{self.author}', {self.pages})"
    
    def __len__(self):  # Длина объекта
        return self.pages
    
    def __eq__(self, other):  # Сравнение на равенство
        if isinstance(other, Book):
            return self.title == other.title and self.author == other.author
        return False

book1 = Book("1984", "Джордж Оруэлл", 328)
book2 = Book("1984", "Джордж Оруэлл", 300)

print(str(book1))    # 1984 - Джордж Оруэлл
print(repr(book1))   # Book('1984', 'Джордж Оруэлл', 328)
print(len(book1))    # 328
print(book1 == book2)  # True
```
---
#### 🔹 Методы класса

В определении класса предполагается, что все функции работают с экземпляром, который всегда передается в первом параметре self. Но сам класс тоже представлен объектом, который может нести состояние и оперировать им.
Переменные класса определяются за пределами обычного метода `__init__()`. Для их изменения нужно использовать класс, а не `self`
**Методы класса** - применяется к самому классу, а не к экземплярам. Обычно методы класса используются для определения альтернативных конструкторов экземпляров.

```python
class MathHelper:
    pi = 3.14159
    
    @classmethod
    def get_pi(cls):  # Метод класса
        return cls.pi
    
    @classmethod
    def create_circle_area_calculator(cls, radius):
        return cls.pi * radius * radius

# Использование без создания экземпляра
print(MathHelper.add(5, 3))        # 8
print(MathHelper.get_pi())         # 3.14159
print(MathHelper.create_circle_area_calculator(5))  # 78.53975
```

**Ниже примеры по методам класса**
**Зачем нужны методы класса**:
- **Альтернативные конструкторы** - создание объектов разными способами
- **Валидация перед созданием** - проверка данных в фабричных методах
- **Работа с атрибутами класса** - управление общими данными
- **Паттерны проектирования** - Singleton, Factory, Builder
- **Чистый код** - логичная группировка функциональности
##### 🔸 **Альтернативные конструкторы**
```python
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    @classmethod
    def from_birth_year(cls, name, birth_year):
        """Создание пользователя по году рождения"""
        current_year = 2024
        age = current_year - birth_year
        return cls(name, age)  # Вызываем основной конструктор
    
    @classmethod
    def from_string(cls, user_string):
        """Создание из строки 'Имя,Возраст'"""
        name, age = user_string.split(',')
        return cls(name, int(age))

# Использование
user1 = User("Алиса", 25)                    # Обычный конструктор
user2 = User.from_birth_year("Боб", 1990)    # Альтернативный конструктор
user3 = User.from_string("Чарли,30")         # Еще один альтернативный
```

##### 🔸 **Работа с атрибутами класса**
```python
class User:
    total_users = 0  # Атрибут класса
    
    def __init__(self, name):
        self.name = name
        User.total_users += 1
    
    @classmethod
    def get_total_users(cls):
        """Получение общего количества пользователей"""
        return cls.total_users
    
    @classmethod
    def reset_counter(cls):
        """Сброс счетчика"""
        cls.total_users = 0

# Использование
print(User.get_total_users())  # 0

user1 = User("Алиса")
user2 = User("Боб")

print(User.get_total_users())  # 2
```
---
#### 🔹 Cтатические методы

>Иногда класс просто используется как пространство имен для функций, объявленных как статические методы с использованием `@staticmethod`. В отличие от обычного метода или метода класса, статический не получает дополнительный аргумент `self` или `cls`. Это обычная функция, которая определяется внутри класса

```python
class Ops:

	@staticmethod
	def add(x, y):
		return x + y

	@staticmethod
	def sub(x, y):
		return x - y

# используется прямой вызов функций через класс

a = Ops.add(2, 3) # a = 5
b = Ops.sub(4, 5) # a = -1
```
---
#### 🔹 Свойства (Properties)

>Свойства (Properties) — это механизм Python, который позволяет использовать методы как обычные атрибуты. Это обеспечивает контролируемый доступ к данным класса через getter'ы, setter'ы и deleter'ы.

```python
class Temperature:
    """Класс для работы с температурой в Цельсиях и Фаренгейтах"""
    
    def __init__(self, celsius=0):
        self._celsius = celsius
    
    @property
    def celsius(self):
        """Getter для температуры в Цельсиях"""
        print("🌡️ Получение температуры в Цельсиях")
        return self._celsius
    
    @celsius.setter
    def celsius(self, value):
        """Setter для температуры в Цельсиях"""
        print(f"🔥 Установка температуры: {value}°C")
        if value < -273.15:
            raise ValueError("Температура не может быть ниже абсолютного нуля (-273.15°C)")
        self._celsius = value
    
    @celsius.deleter
    def celsius(self):
        """Deleter для температуры"""
        print("🗑️ Удаление данных о температуре")
        self._celsius = 0
    
    @property
    def fahrenheit(self):
        """Вычисляемое свойство - температура в Фаренгейтах"""
        print("🇺🇸 Конвертация в Фаренгейты")
        return (self._celsius * 9/5) + 32
    
    @fahrenheit.setter
    def fahrenheit(self, value):
        """Setter для температуры в Фаренгейтах"""
        print(f"🇺🇸 Установка температуры: {value}°F")
        celsius_value = (value - 32) * 5/9
        self.celsius = celsius_value  # Используем уже существующий setter
    
    def __str__(self):
        return f"Temperature({self._celsius}°C / {self.fahrenheit:.1f}°F)"

def demonstrate_basic_properties():
    """Демонстрация базового использования properties"""
    
    print("=== Базовые Properties ===")
    
    # Создание объекта
    temp = Temperature(25)
    print(f"Создан объект: {temp}")
    
    print("\n1. Чтение свойств:")
    print(f"   Цельсии: {temp.celsius}°C")        # Вызывает getter
    print(f"   Фаренгейты: {temp.fahrenheit}°F")  # Вычисляется на лету
    
    print("\n2. Изменение через свойства:")
    temp.celsius = 30          # Вызывает setter с валидацией
    print(f"   После изменения: {temp}")
    
    temp.fahrenheit = 100      # Конвертируется и устанавливается в Цельсиях
    print(f"   После установки °F: {temp}")
    
    print("\n3. Валидация:")
    try:
        temp.celsius = -300    # Попытка установить некорректное значение
    except ValueError as e:
        print(f"   ❌ Ошибка валидации: {e}")
    
    print("\n4. Удаление свойства:")
    del temp.celsius          # Вызывает deleter
    print(f"   После удаления: {temp}")

demonstrate_basic_properties()
```

##### 🔸 Практические применения Properties
```python
import datetime
import hashlib
import json
from typing import List, Dict, Optional

class User:
    """Модель пользователя с полной валидацией"""
    
    def __init__(self, username: str, email: str, password: str):
        self._username = None
        self._email = None
        self._password_hash = None
        self._created_at = datetime.datetime.now()
        self._last_login = None
        self._is_active = True
        self._failed_login_attempts = 0
        self._profile_data = {}
        
        # Используем properties для установки с валидацией
        self.username = username
        self.email = email
        self.password = password
    
    @property
    def username(self) -> str:
        return self._username
    
    @username.setter
    def username(self, value: str):
        if not isinstance(value, str):
            raise TypeError("Имя пользователя должно быть строкой")
        
        value = value.strip()
        if len(value) < 3 or len(value) > 50:
            raise ValueError("Имя пользователя должно содержать от 3 до 50 символов")
        
        if not re.match(r'^[a-zA-Z0-9_-]+$', value):
            raise ValueError("Имя пользователя может содержать только буквы, цифры, _ и -")
        
        self._username = value
    
    @property
    def email(self) -> str:
        return self._email
    
    @email.setter
    def email(self, value: str):
        if not isinstance(value, str):
            raise TypeError("Email должен быть строкой")
        
        value = value.strip().lower()
        if not re.match(r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$', value):
            raise ValueError("Некорректный формат email")
        
        self._email = value
    
    @property
    def password(self) -> str:
        raise AttributeError("Пароль недоступен для чтения")
    
    @password.setter
    def password(self, value: str):
        if not isinstance(value, str):
            raise TypeError("Пароль должен быть строкой")
        
        if len(value) < 8:
            raise ValueError("Пароль должен содержать минимум 8 символов")
        
        if not re.search(r'[A-Z]', value):
            raise ValueError("Пароль должен содержать заглавную букву")
        
        if not re.search(r'[a-z]', value):
            raise ValueError("Пароль должен содержать строчную букву")
        
        if not re.search(r'\d', value):
            raise ValueError("Пароль должен содержать цифру")
        
        # Хешируем пароль
        self._password_hash = hashlib.sha256(value.encode()).hexdigest()
    
    @property
    def created_at(self) -> datetime.datetime:
        return self._created_at
    
    @property
    def last_login(self) -> Optional[datetime.datetime]:
        return self._last_login
    
    @property
    def is_active(self) -> bool:
        return self._is_active
    
    @is_active.setter
    def is_active(self, value: bool):
        if not isinstance(value, bool):
            raise TypeError("is_active должно быть булевым значением")
        self._is_active = value
    
    @property
    def is_locked(self) -> bool:
        """Заблокирован ли аккаунт из-за неудачных попыток входа"""
        return self._failed_login_attempts >= 5
    
    @property
    def days_since_creation(self) -> int:
        """Количество дней с момента создания аккаунта"""
        delta = datetime.datetime.now() - self._created_at
        return delta.days
    
    @property
    def profile_completion(self) -> float:
        """Процент заполненности профиля"""
        required_fields = ['first_name', 'last_name', 'birth_date', 'phone', 'address']
        filled_fields = sum(1 for field in required_fields if self._profile_data.get(field))
        return (filled_fields / len(required_fields)) * 100
    
    def verify_password(self, password: str) -> bool:
        """Проверка пароля"""
        if self.is_locked:
            raise RuntimeError("Аккаунт заблокирован из-за множественных неудачных попыток входа")
        
        password_hash = hashlib.sha256(password.encode()).hexdigest()
        is_valid = password_hash == self._password_hash
        
        if is_valid:
            self._failed_login_attempts = 0
            self._last_login = datetime.datetime.now()
        else:
            self._failed_login_attempts += 1
        
        return is_valid
    
    def unlock_account(self):
        """Разблокировка аккаунта (административная функция)"""
        self._failed_login_attempts = 0
    
    def update_profile(self, **data):
        """Обновление данных профиля"""
        allowed_fields = [
            'first_name', 'last_name', 'birth_date', 'phone', 
            'address', 'bio', 'website', 'avatar_url'
        ]
        
        for field, value in data.items():
            if field in allowed_fields:
                self._profile_data[field] = value
    
    def get_profile_data(self) -> Dict:
        """Получение данных профиля"""
        return self._profile_data.copy()
    
    def to_dict(self) -> Dict:
        """Сериализация в словарь (без чувствительных данных)"""
        return {
            'username': self.username,
            'email': self.email,
            'created_at': self.created_at.isoformat(),
            'last_login': self.last_login.isoformat() if self.last_login else None,
            'is_active': self.is_active,
            'is_locked': self.is_locked,
            'days_since_creation': self.days_since_creation,
            'profile_completion': self.profile_completion,
            'profile_data': self._profile_data
        }
    
    def __str__(self):
        status = "заблокирован" if self.is_locked else ("активен" if self.is_active else "неактивен")
        return f"User({self.username} <{self.email}> - {status})"

### 2. Конфигурационный класс

class Configuration:
    """Класс конфигурации приложения с properties"""
    
    def __init__(self):
        self._debug = False
        self._database_url = "sqlite:///app.db"
        self._secret_key = None
        self._max_connections = 100
        self._cache_timeout = 300
        self._log_level = "INFO"
        self._allowed_hosts = ['localhost']
        self._feature_flags = {}
    
    @property
    def debug(self) -> bool:
        return self._debug
    
    @debug.setter
    def debug(self, value: bool):
        if not isinstance(value, bool):
            raise TypeError("debug должно быть булевым значением")
        self._debug = value
        
        # При включении debug режима изменяем другие настройки
        if value:
            self._log_level = "DEBUG"
    
    @property
    def database_url(self) -> str:
        return self._database_url
    
    @database_url.setter
    def database_url(self, value: str):
        if not isinstance(value, str) or not value.strip():
            raise ValueError("database_url должен быть непустой строкой")
        
        # Проверяем корректность URL базы данных
        valid_prefixes = ['sqlite:///', 'postgresql://', 'mysql://', 'mongodb://']
        if not any(value.startswith(prefix) for prefix in valid_prefixes):
            raise ValueError(f"Неподдерживаемый тип базы данных. Поддерживаются: {valid_prefixes}")
        
        self._database_url = value.strip()
    
    @property
    def secret_key(self) -> Optional[str]:
        return self._secret_key
    
    @secret_key.setter
    def secret_key(self, value: str):
        if not isinstance(value, str):
            raise TypeError("secret_key должен быть строкой")
        
        if len(value) < 32:
            raise ValueError("secret_key должен содержать минимум 32 символа")
        
        self._secret_key = value
    
    @property
    def max_connections(self) -> int:
        return self._max_connections
    
    @max_connections.setter
    def max_connections(self, value: int):
        if not isinstance(value, int):
            raise TypeError("max_connections должно быть целым числом")
        
        if value < 1 or value > 10000:
            raise ValueError("max_connections должно быть от 1 до 10000")
        
        self._max_connections = value
    
    @property
    def cache_timeout(self) -> int:
        return self._cache_timeout
    
    @cache_timeout.setter
    def cache_timeout(self, value: int):
        if not isinstance(value, int):
            raise TypeError("cache_timeout должно быть целым числом")
        
        if value < 0:
            raise ValueError("cache_timeout не может быть отрицательным")
        
        self._cache_timeout = value
    
    @property
    def log_level(self) -> str:
        return self._log_level
    
    @log_level.setter
    def log_level(self, value: str):
        valid_levels = ['DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL']
        
        if not isinstance(value, str):
            raise TypeError("log_level должен быть строкой")
        
        value = value.upper()
        if value not in valid_levels:
            raise ValueError(f"log_level должен быть одним из: {valid_levels}")
        
        self._log_level = value
    
    @property
    def is_production(self) -> bool:
        """Определяет, работает ли приложение в продакшене"""
        return not self.debug and self.secret_key is not None
    
    @property
    def database_type(self) -> str:
        """Тип используемой базы данных"""
        if self.database_url.startswith('sqlite'):
            return 'SQLite'
        elif self.database_url.startswith('postgresql'):
            return 'PostgreSQL'
        elif self.database_url.startswith('mysql'):
            return 'MySQL'
        elif self.database_url.startswith('mongodb'):
            return 'MongoDB'
        else:
            return 'Unknown'
    
    def set_feature_flag(self, flag_name: str, enabled: bool):
        """Установка флага функциональности"""
        if not isinstance(flag_name, str) or not flag_name.strip():
            raise ValueError("Имя флага должно быть непустой строкой")
        
        if not isinstance(enabled, bool):
            raise TypeError("Значение флага должно быть булевым")
        
        self._feature_flags[flag_name] = enabled
    
    def is_feature_enabled(self, flag_name: str) -> bool:
        """Проверка, включена ли функция"""
        return self._feature_flags.get(flag_name, False)
    
    def load_from_dict(self, config_dict: Dict):
        """Загрузка конфигурации из словаря"""
        property_mapping = {
            'debug': 'debug',
            'database_url': 'database_url',
            'secret_key': 'secret_key',
            'max_connections': 'max_connections',
            'cache_timeout': 'cache_timeout',
            'log_level': 'log_level'
        }
        
        for key, property_name in property_mapping.items():
            if key in config_dict:
                setattr(self, property_name, config_dict[key])
        
        # Загружаем флаги функций
        if 'feature_flags' in config_dict:
            for flag_name, enabled in config_dict['feature_flags'].items():
                self.set_feature_flag(flag_name, enabled)
    
    def to_dict(self) -> Dict:
        """Экспорт конфигурации в словарь"""
        return {
            'debug': self.debug,
            'database_url': self.database_url,
            'secret_key': self.secret_key,
            'max_connections': self.max_connections,
            'cache_timeout': self.cache_timeout,
            'log_level': self.log_level,
            'is_production': self.is_production,
            'database_type': self.database_type,
            'feature_flags': self._feature_flags.copy()
        }
    
    def __str__(self):
        return f"Configuration(debug={self.debug}, db={self.database_type}, prod={self.is_production})"

def demonstrate_practical_applications():
    """Демонстрация практического применения Properties"""
    
    print("\n=== Практические применения Properties ===")
    
    print("1. Модель пользователя с валидацией:")
    
    try:
        user = User("john_doe", "john@example.com", "SecurePass123")
        print(f"   ✅ Создан пользователь: {user}")
        
        # Обновляем профиль
        user.update_profile(
            first_name="John",
            last_name="Doe",
            birth_date="1990-01-15",
            phone="+1234567890"
        )
        
        print(f"   Заполненность профиля: {user.profile_completion:.1f}%")
        print(f"   Дней с создания: {user.days_since_creation}")
        
    except (ValueError, TypeError) as e:
        print(f"   ❌ Ошибка создания пользователя: {e}")
    
    print("\n2. Тестирование валидации пользователя:")
    
    invalid_cases = [
        ("Короткое имя", lambda: User("ab", "test@example.com", "ValidPass123")),
        ("Неверный email", lambda: User("valid_user", "invalid-email", "ValidPass123")),
        ("Слабый пароль", lambda: User("valid_user", "test@example.com", "weak")),
    ]
    
    for description, create_func in invalid_cases:
        try:
            create_func()
            print(f"   ⚠️ {description}: валидация не сработала!")
        except (ValueError, TypeError) as e:
            print(f"   ✅ {description}: {e}")
    
    print("\n3. Аутентификация:")
    
    # Правильный пароль
    if user.verify_password("SecurePass123"):
        print(f"   ✅ Успешная аутентификация")
        print(f"   Последний вход: {user.last_login}")
    
    # Неправильные пароли
    for i in range(6):
        try:
            result = user.verify_password("wrong_password")
            if not result:
                print(f"   ❌ Неудачная попытка входа #{i+1}")
        except RuntimeError as e:
            print(f"   🔒 {e}")
            break
    
    print(f"   Аккаунт заблокирован: {user.is_locked}")
    
    # Разблокируем для дальнейших тестов
    user.unlock_account()
    
    print("\n4. Конфигурация приложения:")
    
    config = Configuration()
    print(f"   Базовая конфигурация: {config}")
    
    # Загрузка из словаря
    config_data = {
        'debug': True,
        'database_url': 'postgresql://user:pass@localhost/myapp',
        'secret_key': 'super-secret-key-with-32-characters-minimum',
        'max_connections': 50,
        'cache_timeout': 600,
        'log_level': 'DEBUG',
        'feature_flags': {
            'new_ui': True,
            'beta_feature': False,
            'advanced_search': True
        }
    }
    
    config.load_from_dict(config_data)
    print(f"   После загрузки: {config}")
    print(f"   Тип БД: {config.database_type}")
    print(f"   Продакшен режим: {config.is_production}")
    
    print(f"\n   Флаги функций:")
    for flag in ['new_ui', 'beta_feature', 'advanced_search', 'non_existent']:
        status = "включена" if config.is_feature_enabled(flag) else "выключена"
        print(f"     {flag}: {status}")
    
    print("\n5. Валидация конфигурации:")
    
    test_configs = [
        ("Некорректный URL БД", lambda: setattr(config, 'database_url', 'invalid://url')),
        ("Отрицательные подключения", lambda: setattr(config, 'max_connections', -1)),
        ("Короткий secret_key", lambda: setattr(config, 'secret_key', 'short')),
        ("Неверный log_level", lambda: setattr(config, 'log_level', 'INVALID')),
    ]
    
    for description, test_func in test_configs:
        try:
            test_func()
            print(f"   ⚠️ {description}: валидация не сработала!")
        except (ValueError, TypeError) as e:
            print(f"   ✅ {description}: {e}")
    
    print("\n6. Сериализация данных:")
    
    # Пользователь
    user_data = user.to_dict()
    print(f"   Данные пользователя:")
    for key, value in user_data.items():
        if isinstance(value, dict) and not value:
            continue
        print(f"     {key}: {value}")
    
    # Конфигурация
    config_export = config.to_dict()
    print(f"\n   Экспорт конфигурации:")
    for key, value in config_export.items():
        if key == 'secret_key':
            value = "*" * len(str(value)) if value else None
        print(f"     {key}: {value}")

demonstrate_practical_applications()
```

##### 🔸 Лучшие практики

**✅ Используйте Properties для:**
- Валидации входных данных
- Вычисляемых атрибутов
- Логирования доступа к данным
- Ленивой инициализации
- Трансформации данных
- Контроля доступа

**❌ Избегайте Properties для:**
- Простых атрибутов без логики
- Очень дорогих операций в getter'ах
- Сложной бизнес-логики (лучше использовать методы)
- Частых изменений (кэш может устареть)

 **Паттерны использования**:

**1. Read-Only Properties**:
```python
@property  
def created_at(self):  
    return self._created_at
```

**2. Валидирующие Properties**:
```python
@property  
def age(self):  
    return self._age

@age.setter  
def age(self, value):  
    if not 0 <= value <= 150:  
        raise ValueError("Некорректный возраст")  
    self._age = value
```

**3. Связанные Properties**:
```python
@property  
def fahrenheit(self):  
    return self.celsius * 9/5 + 32  
  
@fahrenheit.setter  
def fahrenheit(self, value):  
    self.celsius = (value - 32) * 5/9
```

**4. Кэширующие Properties**:
```python
@property  
def expensive_calculation(self):  
    if not hasattr(self, '_cached_result'):  
        self._cached_result = self._do_calculation()  
    return self._cached_result
```
---
#### 🔹 Абстрактные классы и интерфейсы

> Абстрактные классы и интерфейсы — это механизмы для определения контрактов (договоров) в объектно-ориентированном программировании. Они задают, какие методы должны быть реализованы в наследующих классах, обеспечивая единообразие архитектуры. Абстрактный класс не предназначен для прямого создания экземпляров. Хотя абстрактный класс нельзя создать, он может определять методы и свойства для использования в субклассах. Более того, абстрактные методы в базовом классе могут вызываться из них.

В нём есть специальный декоратор `@abstractmethod`, которым отмечают методы, обязательные для переопределения в наследниках
##### 🔸 Модуль `abc` - Abstract Base Classes
```python
from abc import ABC, abstractmethod, abstractproperty, abstractclassmethod, abstractstaticmethod
import math
from typing import Any, List, Dict, Optional

# Способ 1: наследование от ABC
class Shape(ABC):
    """Абстрактный базовый класс для геометрических фигур"""
    
    def __init__(self, name: str):
        self.name = name
    
    @abstractmethod
    def area(self) -> float:
        """Абстрактный метод для вычисления площади"""
        pass
    
    @abstractmethod
    def perimeter(self) -> float:
        """Абстрактный метод для вычисления периметра"""
        pass
    
    @abstractmethod
    def draw(self) -> None:
        """Абстрактный метод для отрисовки фигуры"""
        pass
    
    # Конкретный метод (можно использовать в наследниках)
    def describe(self) -> str:
        """Описание фигуры с её характеристиками"""
        return f"{self.name}: площадь={self.area():.2f}, периметр={self.perimeter():.2f}"
    
    # Абстрактный метод класса
    @abstractclassmethod
    def from_string(cls, shape_string: str):
        """Создание фигуры из строкового представления"""
        pass
    
    # Абстрактный статический метод
    @abstractstaticmethod
    def validate_dimensions(*args) -> bool:
        """Валидация размеров фигуры"""
        pass
    
    def __str__(self):
        return f"Shape(name='{self.name}')"

# Способ 2: использование метакласса ABCMeta
from abc import ABCMeta

class Vehicle(metaclass=ABCMeta):
    """Абстрактный класс транспортного средства"""
    
    def __init__(self, brand: str, model: str):
        self.brand = brand
        self.model = model
        self._is_running = False
    
    @abstractmethod
    def start_engine(self) -> None:
        """Запуск двигателя"""
        pass
    
    @abstractmethod
    def stop_engine(self) -> None:
        """Остановка двигателя"""
        pass
    
    @abstractmethod
    def get_max_speed(self) -> int:
        """Максимальная скорость"""
        pass
    
    # Конкретная реализация
    def get_info(self) -> str:
        status = "работает" if self._is_running else "заглушен"
        return f"{self.brand} {self.model} (двигатель {status})"
    
    @property
    def is_running(self) -> bool:
        return self._is_running

def demonstrate_abstract_basics():
    """Демонстрация основ абстрактных классов"""
    
    print("=== Основы абстрактных классов ===")
    
    print("1. Попытка создать экземпляр абстрактного класса:")
    try:
        # Это вызовет ошибку
        shape = Shape("test")
    except TypeError as e:
        print(f"   ❌ {e}")
    
    try:
        # И это тоже
        vehicle = Vehicle("Toyota", "Camry")
    except TypeError as e:
        print(f"   ❌ {e}")
    
    print("\n2. Создание конкретных реализаций:")
    
    # Реализация для фигур
    class Circle(Shape):
        def __init__(self, radius: float):
            super().__init__("Круг")
            if radius <= 0:
                raise ValueError("Радиус должен быть положительным")
            self.radius = radius
        
        def area(self) -> float:
            return math.pi * self.radius ** 2
        
        def perimeter(self) -> float:
            return 2 * math.pi * self.radius
        
        def draw(self) -> None:
            print(f"   🔴 Рисуем круг радиусом {self.radius}")
        
        @classmethod
        def from_string(cls, shape_string: str):
            # Ожидаем формат "circle:5.0"
            parts = shape_string.split(':')
            if len(parts) != 2 or parts[0] != 'circle':
                raise ValueError("Неверный формат строки для круга")
            return cls(float(parts[1]))
        
        @staticmethod
        def validate_dimensions(*args) -> bool:
            return len(args) == 1 and args[0] > 0
        
        def __str__(self):
            return f"Circle(radius={self.radius})"
    
    class Rectangle(Shape):
        def __init__(self, width: float, height: float):
            super().__init__("Прямоугольник")
            if not self.validate_dimensions(width, height):
                raise ValueError("Размеры должны быть положительными")
            self.width = width
            self.height = height
        
        def area(self) -> float:
            return self.width * self.height
        
        def perimeter(self) -> float:
            return 2 * (self.width + self.height)
        
        def draw(self) -> None:
            print(f"   ▬ Рисуем прямоугольник {self.width}x{self.height}")
        
        @classmethod
        def from_string(cls, shape_string: str):
            # Ожидаем формат "rectangle:4.0:6.0"
            parts = shape_string.split(':')
            if len(parts) != 3 or parts[0] != 'rectangle':
                raise ValueError("Неверный формат строки для прямоугольника")
            return cls(float(parts[1]), float(parts[2]))
        
        @staticmethod
        def validate_dimensions(*args) -> bool:
            return len(args) == 2 and all(arg > 0 for arg in args)
        
        def __str__(self):
            return f"Rectangle(width={self.width}, height={self.height})"
    
    # Тестирование
    circle = Circle(5.0)
    rectangle = Rectangle(4.0, 6.0)
    
    shapes = [circle, rectangle]
    
    for shape in shapes:
        print(f"   {shape}")
        print(f"     {shape.describe()}")
        shape.draw()
    
    print("\n3. Использование фабричных методов:")
    circle_from_string = Circle.from_string("circle:3.0")
    rectangle_from_string = Rectangle.from_string("rectangle:2.0:8.0")
    
    print(f"   Из строки: {circle_from_string}")
    print(f"   Из строки: {rectangle_from_string}")
    
    print("\n4. Валидация размеров:")
    print(f"   Circle.validate_dimensions(5.0): {Circle.validate_dimensions(5.0)}")
    print(f"   Circle.validate_dimensions(-1.0): {Circle.validate_dimensions(-1.0)}")
    print(f"   Rectangle.validate_dimensions(4.0, 6.0): {Rectangle.validate_dimensions(4.0, 6.0)}")

demonstrate_abstract_basics()
```

##### 🔸 Абстрактные свойства и сложная иерархия
```python
from abc import ABC, abstractmethod
from typing import Protocol, runtime_checkable
import datetime
from enum import Enum

class UserRole(Enum):
    ADMIN = "admin"
    USER = "user"
    MODERATOR = "moderator"
    GUEST = "guest"

class User(ABC):
    """Абстрактный базовый класс пользователя"""
    
    def __init__(self, username: str, email: str):
        self._username = username
        self._email = email
        self._created_at = datetime.datetime.now()
        self._last_login = None
    
    # Абстрактные свойства
    @property
    @abstractmethod
    def role(self) -> UserRole:
        """Роль пользователя"""
        pass
    
    @property
    @abstractmethod
    def permissions(self) -> List[str]:
        """Список разрешений пользователя"""
        pass
    
    @property
    @abstractmethod
    def display_name(self) -> str:
        """Отображаемое имя"""
        pass
    
    # Абстрактные методы
    @abstractmethod
    def can_access(self, resource: str) -> bool:
        """Проверка доступа к ресурсу"""
        pass
    
    @abstractmethod
    def authenticate(self, password: str) -> bool:
        """Аутентификация пользователя"""
        pass
    
    @abstractmethod
    def get_profile_data(self) -> Dict[str, Any]:
        """Получение данных профиля"""
        pass
    
    # Конкретные методы (доступны всем наследникам)
    @property
    def username(self) -> str:
        return self._username
    
    @property
    def email(self) -> str:
        return self._email
    
    @property
    def created_at(self) -> datetime.datetime:
        return self._created_at
    
    @property
    def last_login(self) -> Optional[datetime.datetime]:
        return self._last_login
    
    def login(self) -> None:
        """Отметка о входе в систему"""
        self._last_login = datetime.datetime.now()
    
    def days_since_registration(self) -> int:
        """Дни с момента регистрации"""
        return (datetime.datetime.now() - self._created_at).days
    
    def __str__(self):
        return f"User(username='{self.username}', role='{self.role.value}')"

class AdminUser(User):
    """Администратор системы"""
    
    def __init__(self, username: str, email: str, admin_level: int = 1):
        super().__init__(username, email)
        self.admin_level = admin_level
        self._password_hash = None
    
    @property
    def role(self) -> UserRole:
        return UserRole.ADMIN
    
    @property
    def permissions(self) -> List[str]:
        base_permissions = [
            "read_all", "write_all", "delete_all", 
            "manage_users", "system_config", "view_logs"
        ]
        if self.admin_level >= 2:
            base_permissions.extend(["database_access", "server_management"])
        if self.admin_level >= 3:
            base_permissions.extend(["root_access", "security_settings"])
        return base_permissions
    
    @property
    def display_name(self) -> str:
        level_suffix = f" (уровень {self.admin_level})" if self.admin_level > 1 else ""
        return f"Администратор {self.username}{level_suffix}"
    
    def can_access(self, resource: str) -> bool:
        # Админы имеют доступ ко всему
        restricted_resources = ["root_shell", "database_raw"] if self.admin_level < 3 else []
        return resource not in restricted_resources
    
    def authenticate(self, password: str) -> bool:
        # Упрощенная проверка пароля
        import hashlib
        if self._password_hash is None:
            return False
        
        password_hash = hashlib.sha256(password.encode()).hexdigest()
        return password_hash == self._password_hash
    
    def set_password(self, password: str) -> None:
        """Установка пароля"""
        import hashlib
        self._password_hash = hashlib.sha256(password.encode()).hexdigest()
    
    def get_profile_data(self) -> Dict[str, Any]:
        return {
            "username": self.username,
            "email": self.email,
            "role": self.role.value,
            "admin_level": self.admin_level,
            "permissions_count": len(self.permissions),
            "created_at": self.created_at.isoformat(),
            "last_login": self.last_login.isoformat() if self.last_login else None
        }
    
    def manage_user(self, user: 'User', action: str) -> bool:
        """Управление пользователями"""
        if not self.can_access("manage_users"):
            return False
        
        allowed_actions = ["activate", "deactivate", "delete", "modify"]
        if action not in allowed_actions:
            return False
        
        print(f"👨‍💼 {self.display_name} выполняет '{action}' для {user.username}")
        return True

class RegularUser(User):
    """Обычный пользователь"""
    
    def __init__(self, username: str, email: str, first_name: str = "", last_name: str = ""):
        super().__init__(username, email)
        self.first_name = first_name
        self.last_name = last_name
        self._password_hash = None
        self._is_active = True
    
    @property
    def role(self) -> UserRole:
        return UserRole.USER
    
    @property
    def permissions(self) -> List[str]:
        return ["read_own", "write_own", "update_profile"] if self._is_active else ["read_own"]
    
    @property
    def display_name(self) -> str:
        if self.first_name and self.last_name:
            return f"{self.first_name} {self.last_name}"
        return self.username
    
    @property
    def is_active(self) -> bool:
        return self._is_active
    
    def can_access(self, resource: str) -> bool:
        if not self._is_active:
            return resource == "login"
        
        own_resources = [f"user_data_{self.username}", f"profile_{self.username}"]
        public_resources = ["public_content", "help", "about"]
        
        return resource in own_resources or resource in public_resources
    
    def authenticate(self, password: str) -> bool:
        if not self._is_active:
            return False
        
        import hashlib
        if self._password_hash is None:
            return False
        
        password_hash = hashlib.sha256(password.encode()).hexdigest()
        return password_hash == self._password_hash
    
    def set_password(self, password: str) -> None:
        """Установка пароля"""
        if len(password) < 6:
            raise ValueError("Пароль должен содержать минимум 6 символов")
        
        import hashlib
        self._password_hash = hashlib.sha256(password.encode()).hexdigest()
    
    def get_profile_data(self) -> Dict[str, Any]:
        return {
            "username": self.username,
            "email": self.email,
            "role": self.role.value,
            "first_name": self.first_name,
            "last_name": self.last_name,
            "display_name": self.display_name,
            "is_active": self.is_active,
            "created_at": self.created_at.isoformat(),
            "last_login": self.last_login.isoformat() if self.last_login else None
        }
    
    def deactivate(self) -> None:
        """Деактивация аккаунта"""
        self._is_active = False
    
    def activate(self) -> None:
        """Активация аккаунта"""
        self._is_active = True

class GuestUser(User):
    """Гостевой пользователь"""
    
    def __init__(self):
        import uuid
        guest_id = str(uuid.uuid4())[:8]
        super().__init__(f"guest_{guest_id}", f"guest_{guest_id}@temp.local")
    
    @property
    def role(self) -> UserRole:
        return UserRole.GUEST
    
    @property
    def permissions(self) -> List[str]:
        return ["read_public", "view_content"]
    
    @property
    def display_name(self) -> str:
        return "Гость"
    
    def can_access(self, resource: str) -> bool:
        public_resources = ["public_content", "help", "about", "register", "login"]
        return resource in public_resources
    
    def authenticate(self, password: str) -> bool:
        return False  # Гости не могут аутентифицироваться
    
    def get_profile_data(self) -> Dict[str, Any]:
        return {
            "username": self.username,
            "role": self.role.value,
            "display_name": self.display_name,
            "is_guest": True,
            "created_at": self.created_at.isoformat()
        }

def demonstrate_abstract_hierarchy():
    """Демонстрация иерархии абстрактных классов"""
    
    print("\n=== Иерархия абстрактных классов ===")
    
    print("1. Создание пользователей разных типов:")
    
    admin = AdminUser("admin", "admin@company.com", admin_level=2)
    admin.set_password("secure_admin_password")
    
    user = RegularUser("john_doe", "john@example.com", "Джон", "Доу")
    user.set_password("user_password")
    
    guest = GuestUser()
    
    users = [admin, user, guest]
    
    for u in users:
        print(f"   {u}")
        print(f"     Отображаемое имя: {u.display_name}")
        print(f"     Разрешений: {len(u.permissions)}")
    
    print("\n2. Тестирование доступа к ресурсам:")
    
    resources = ["public_content", "user_data_john_doe", "manage_users", "system_config", "root_shell"]
    
    for resource in resources:
        print(f"\n   Ресурс: {resource}")
        for u in users:
            access = "✅" if u.can_access(resource) else "❌"
            print(f"     {u.role.value}: {access}")
    
    print("\n3. Аутентификация:")
    
    # Правильные пароли
    print("   Правильные пароли:")
    admin_auth = admin.authenticate("secure_admin_password")
    user_auth = user.authenticate("user_password")
    guest_auth = guest.authenticate("any_password")
    
    print(f"     Админ: {'✅' if admin_auth else '❌'}")
    print(f"     Пользователь: {'✅' if user_auth else '❌'}")
    print(f"     Гость: {'✅' if guest_auth else '❌'}")
    
    # После аутентификации отмечаем вход
    if admin_auth:
        admin.login()
    if user_auth:
        user.login()
    
    print("\n4. Данные профилей:")
    
    for u in users:
        profile = u.get_profile_data()
        print(f"\n   Профиль {u.username}:")
        for key, value in profile.items():
            print(f"     {key}: {value}")
    
    print("\n5. Административные действия:")
    
    # Админ управляет пользователями
    admin.manage_user(user, "modify")
    admin.manage_user(guest, "delete")
    
    # Обычный пользователь пытается управлять (не должно работать)
    if hasattr(user, 'manage_user'):
        user.manage_user(guest, "delete")
    else:
        print(f"   ❌ {user.username} не может управлять пользователями")

demonstrate_abstract_hierarchy()
```

##### 🔸 Протоколы (Protocols) - Структурная типизация
```python
from typing import Protocol, runtime_checkable, Union, Any, List
from abc import abstractmethod

# Protocol - это более современный способ определения интерфейсов
@runtime_checkable
class Drawable(Protocol):
    """Протокол для объектов, которые можно рисовать"""
    
    def draw(self) -> None:
        """Нарисовать объект"""
        ...
    
    def get_bounds(self) -> tuple[float, float, float, float]:
        """Получить границы объекта (x, y, width, height)"""
        ...

@runtime_checkable
class Serializable(Protocol):
    """Протокол для сериализуемых объектов"""
    
    def to_dict(self) -> Dict[str, Any]:
        """Преобразование в словарь"""
        ...
    
    @classmethod
    def from_dict(cls, data: Dict[str, Any]) -> 'Serializable':
        """Создание из словаря"""
        ...

@runtime_checkable
class Comparable(Protocol):
    """Протокол для сравниваемых объектов"""
    
    def __eq__(self, other: Any) -> bool:
        ...
    
    def __lt__(self, other: Any) -> bool:
        ...

@runtime_checkable
class Cacheable(Protocol):
    """Протокол для кэшируемых объектов"""
    
    @property
    def cache_key(self) -> str:
        """Ключ для кэширования"""
        ...
    
    @property
    def cache_ttl(self) -> int:
        """Время жизни в кэше (секунды)"""
        ...

# Реализации протоколов без явного наследования
class Image:
    """Класс изображения, реализующий несколько протоколов"""
    
    def __init__(self, filename: str, width: int, height: int):
        self.filename = filename
        self.width = width
        self.height = height
        self.x = 0
        self.y = 0
        self._created_at = datetime.datetime.now()
    
    # Реализация Drawable
    def draw(self) -> None:
        print(f"🖼️  Рисуем изображение {self.filename} ({self.width}x{self.height}) в позиции ({self.x}, {self.y})")
    
    def get_bounds(self) -> tuple[float, float, float, float]:
        return (self.x, self.y, self.width, self.height)
    
    # Реализация Serializable
    def to_dict(self) -> Dict[str, Any]:
        return {
            "type": "image",
            "filename": self.filename,
            "width": self.width,
            "height": self.height,
            "x": self.x,
            "y": self.y,
            "created_at": self._created_at.isoformat()
        }
    
    @classmethod
    def from_dict(cls, data: Dict[str, Any]) -> 'Image':
        image = cls(data["filename"], data["width"], data["height"])
        image.x = data.get("x", 0)
        image.y = data.get("y", 0)
        return image
    
    # Реализация Comparable
    def __eq__(self, other: Any) -> bool:
        if not isinstance(other, Image):
            return False
        return (self.filename == other.filename and 
                self.width == other.width and 
                self.height == other.height)
    
    def __lt__(self, other: Any) -> bool:
        if not isinstance(other, Image):
            return NotImplemented
        return (self.width * self.height) < (other.width * other.height)
    
    # Реализация Cacheable
    @property
    def cache_key(self) -> str:
        return f"image_{self.filename}_{self.width}x{self.height}"
    
    @property
    def cache_ttl(self) -> int:
        return 3600  # 1 час
    
    def move_to(self, x: int, y: int) -> None:
        """Перемещение изображения"""
        self.x, self.y = x, y
    
    def __str__(self):
        return f"Image('{self.filename}', {self.width}x{self.height})"

class TextBox:
    """Текстовое поле"""
    
    def __init__(self, text: str, x: int = 0, y: int = 0, width: int = 100, height: int = 20):
        self.text = text
        self.x = x
        self.y = y
        self.width = width
        self.height = height
        self.font_size = 12
        self._created_at = datetime.datetime.now()
    
    # Реализация Drawable
    def draw(self) -> None:
        print(f"📝 Рисуем текст '{self.text}' в ({self.x}, {self.y}), размер шрифта: {self.font_size}")
    
    def get_bounds(self) -> tuple[float, float, float, float]:
        return (self.x, self.y, self.width, self.height)
    
    # Реализация Serializable
    def to_dict(self) -> Dict[str, Any]:
        return {
            "type": "textbox",
            "text": self.text,
            "x": self.x,
            "y": self.y,
            "width": self.width,
            "height": self.height,
            "font_size": self.font_size,
            "created_at": self._created_at.isoformat()
        }
    
    @classmethod
    def from_dict(cls, data: Dict[str, Any]) -> 'TextBox':
        textbox = cls(data["text"], data["x"], data["y"], data["width"], data["height"])
        textbox.font_size = data.get("font_size", 12)
        return textbox
    
    # Реализация Cacheable
    @property
    def cache_key(self) -> str:
        return f"textbox_{hash(self.text)}_{self.x}_{self.y}"
    
    @property
    def cache_ttl(self) -> int:
        return 1800  # 30 минут
    
    def __str__(self):
        return f"TextBox('{self.text[:20]}...', {self.x}, {self.y})"

class Chart:
    """Диаграмма, реализующая только некоторые протоколы"""
    
    def __init__(self, chart_type: str, data: List[float]):
        self.chart_type = chart_type
        self.data = data
        self.x = 0
        self.y = 0
        self.width = 300
        self.height = 200
    
    # Реализация только Drawable
    def draw(self) -> None:
        print(f"📊 Рисуем {self.chart_type} диаграмму с {len(self.data)} точками данных")
    
    def get_bounds(self) -> tuple[float, float, float, float]:
        return (self.x, self.y, self.width, self.height)
    
    # НЕ реализуем Serializable и Cacheable
    
    def __str__(self):
        return f"Chart({self.chart_type}, {len(self.data)} points)"

# Функции для работы с протоколами
def draw_objects(objects: List[Drawable]) -> None:
    """Рисование всех объектов, реализующих Drawable"""
    print("🎨 Рисуем все объекты:")
    for obj in objects:
        obj.draw()
        bounds = obj.get_bounds()
        print(f"   Границы: x={bounds[0]}, y={bounds[1]}, w={bounds[2]}, h={bounds[3]}")

def serialize_objects(objects: List[Serializable]) -> List[Dict[str, Any]]:
    """Сериализация объектов"""
    print("💾 Сериализуем объекты:")
    result = []
    for obj in objects:
        data = obj.to_dict()
        result.append(data)
        print(f"   {type(obj).__name__}: {len(data)} полей")
    return result

def cache_objects(objects: List[Cacheable]) -> Dict[str, Any]:
    """Кэширование объектов"""
    print("🗄️  Кэшируем объекты:")
    cache = {}
    for obj in objects:
        key = obj.cache_key
        ttl = obj.cache_ttl
        cache[key] = {"object": obj, "ttl": ttl}
        print(f"   {key}: TTL={ttl}s")
    return cache

def sort_comparable_objects(objects: List[Comparable]) -> List[Comparable]:
    """Сортировка сравниваемых объектов"""
    print("🔢 Сортируем объекты:")
    sorted_objects = sorted(objects)
    for i, obj in enumerate(sorted_objects):
        print(f"   {i+1}. {obj}")
    return sorted_objects

def demonstrate_protocols():
    """Демонстрация работы с протоколами"""
    
    print("\n=== Протоколы (Protocols) ===")
    
    print("1. Создание объектов:")
    
    image1 = Image("photo.jpg", 800, 600)
    image1.move_to(10, 20)
    
    image2 = Image("icon.png", 32, 32)
    image2.move_to(100, 100)
    
    textbox = TextBox("Привет, мир!", 50, 50, 200, 30)
    textbox.font_size = 16
    
    chart = Chart("bar", [1.5, 2.3, 3.7, 2.1, 4.2])
    
    all_objects = [image1, image2, textbox, chart]
    
    for obj in all_objects:
        print(f"   {obj}")
    
    print("\n2. Проверка реализации протоколов:")
    
    protocols_check = {
        "Drawable": Drawable,
        "Serializable": Serializable, 
        "Comparable": Comparable,
        "Cacheable": Cacheable
    }
    
    for obj in all_objects:
        print(f"\n   {type(obj).__name__}:")
        for protocol_name, protocol_class in protocols_check.items():
            is_instance = isinstance(obj, protocol_class)
            status = "✅" if is_instance else "❌"
            print(f"     {protocol_name}: {status}")
    
    print("\n3. Использование протоколов:")
    
    # Все объекты можно рисовать
    drawable_objects = [obj for obj in all_objects if isinstance(obj, Drawable)]
    draw_objects(drawable_objects)
    
    print()
    
    # Только некоторые можно сериализовать
    serializable_objects = [obj for obj in all_objects if isinstance(obj, Serializable)]
    serialized_data = serialize_objects(serializable_objects)
    
    print()
    
    # Только некоторые можно кэшировать
    cacheable_objects = [obj for obj in all_objects if isinstance(obj, Cacheable)]
    cache = cache_objects(cacheable_objects)
    
    print()
    
    # Только некоторые можно сравнивать
    comparable_objects = [obj for obj in all_objects if isinstance(obj, Comparable)]
    if comparable_objects:
        sorted_objects = sort_comparable_objects(comparable_objects)
    
    print("\n4. Восстановление из сериализованных данных:")
    
    for data in serialized_data:
        obj_type = data["type"]
        if obj_type == "image":
            restored = Image.from_dict(data)
        elif obj_type == "textbox":
            restored = TextBox.from_dict(data)
        else:
            continue
        
        print(f"   Восстановлен: {restored}")
        
        # Проверяем, что восстановленный объект эквивалентен оригиналу
        for original in all_objects:
            if (isinstance(original, Comparable) and 
                isinstance(restored, type(original)) and 
                original == restored):
                print(f"     ✅ Соответствует оригиналу")
                break

demonstrate_protocols()
```

##### 🔸 Миксины и множественное наследование
```python
from typing import Dict, Any, List, Optional
import json
import pickle
import datetime
from abc import ABC, abstractmethod

# Миксины - классы, предоставляющие дополнительную функциональность
class SerializationMixin:
    """Миксин для сериализации объектов"""
    
    def to_json(self) -> str:
        """Сериализация в JSON"""
        if hasattr(self, 'to_dict'):
            return json.dumps(self.to_dict(), indent=2, ensure_ascii=False)
        else:
            raise NotImplementedError("Класс должен реализовать метод to_dict()")
    
    @classmethod
    def from_json(cls, json_str: str):
        """Десериализация из JSON"""
        data = json.loads(json_str)
        if hasattr(cls, 'from_dict'):
            return cls.from_dict(data)
        else:
            raise NotImplementedError("Класс должен реализовать метод from_dict()")
    
    def to_pickle(self) -> bytes:
        """Сериализация в pickle"""
        return pickle.dumps(self)
    
    @classmethod
    def from_pickle(cls, pickle_data: bytes):
        """Десериализация из pickle"""
        return pickle.loads(pickle_data)
    
    def save_to_file(self, filename: str, format: str = 'json') -> None:
        """Сохранение в файл"""
        if format.lower() == 'json':
            with open(filename, 'w', encoding='utf-8') as f:
                f.write(self.to_json())
        elif format.lower() == 'pickle':
            with open(filename, 'wb') as f:
                f.write(self.to_pickle())
        else:
            raise ValueError("Поддерживаемые форматы: 'json', 'pickle'")
    
    @classmethod
    def load_from_file(cls, filename: str, format: str = 'json'):
        """Загрузка из файла"""
        if format.lower() == 'json':
            with open(filename, 'r', encoding='utf-8') as f:
                return cls.from_json(f.read())
        elif format.lower() == 'pickle':
            with open(filename, 'rb') as f:
                return cls.from_pickle(f.read())
        else:
            raise ValueError("Поддерживаемые форматы: 'json', 'pickle'")

class LoggingMixin:
    """Миксин для логирования действий"""
    
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self._logs: List[Dict[str, Any]] = []
        self.log_action("object_created", {"class": self.__class__.__name__})
    
    def log_action(self, action: str, details: Dict[str, Any] = None) -> None:
        """Логирование действия"""
        log_entry = {
            "timestamp": datetime.datetime.now().isoformat(),
            "action": action,
            "details": details or {}
        }
        self._logs.append(log_entry)
    
    def get_logs(self) -> List[Dict[str, Any]]:
        """Получение всех логов"""
        return self._logs.copy()
    
    def get_recent_logs(self, count: int = 10) -> List[Dict[str, Any]]:
        """Получение последних логов"""
        return self._logs[-count:]
    
    def clear_logs(self) -> None:
        """Очистка логов"""
        self.log_action("logs_cleared")
        self._logs.clear()
    
    def logs_summary(self) -> Dict[str, int]:
        """Сводка по действиям"""
        summary = {}
        for log in self._logs:
            action = log["action"]
            summary[action] = summary.get(action, 0) + 1
        return summary

class ValidationMixin:
    """Миксин для валидации данных"""
    
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self._validation_rules: Dict[str, List] = {}
        self._is_valid = True
        self._validation_errors: List[str] = []
    
    def add_validation_rule(self, field: str, rule_func, error_message: str) -> None:
        """Добавление правила валидации"""
        if field not in self._validation_rules:
            self._validation_rules[field] = []
        
        self._validation_rules[field].append({
            "rule": rule_func,
            "message": error_message
        })
    
    def validate_field(self, field: str, value: Any) -> bool:
        """Валидация конкретного поля"""
        if field not in self._validation_rules:
            return True
        
        field_errors = []
        for rule_data in self._validation_rules[field]:
            rule_func = rule_data["rule"]
            error_message = rule_data["message"]
            
            try:
                if not rule_func(value):
                    field_errors.append(f"{field}: {error_message}")
            except Exception as e:
                field_errors.append(f"{field}: Ошибка валидации - {e}")
        
        return len(field_errors) == 0
    
    def validate_all(self) -> bool:
        """Полная валидация объекта"""
        self._validation_errors.clear()
        
        for field in self._validation_rules:
            if hasattr(self, field):
                value = getattr(self, field)
                if not self.validate_field(field, value):
                    # Ошибки уже добавлены в validate_field
                    pass
        
        self._is_valid = len(self._validation_errors) == 0
        return self._is_valid
    
    @property
    def is_valid(self) -> bool:
        """Проверка валидности объекта"""
        return self._is_valid
    
    @property
    def validation_errors(self) -> List[str]:
        """Список ошибок валидации"""
        return self._validation_errors.copy()

class CacheableMixin:
    """Миксин для кэширования результатов методов"""
    
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self._method_cache: Dict[str, Dict] = {}
        self._cache_stats: Dict[str, Dict[str, int]] = {}
    
    def cached_method(self, cache_key: str = None, ttl: int = 3600):
        """Декоратор для кэширования методов"""
        def decorator(func):
            def wrapper(*args, **kwargs):
                # Генерируем ключ кэша
                key = cache_key or f"{func.__name__}_{hash(str(args) + str(kwargs))}"
                
                # Проверяем статистику
                if func.__name__ not in self._cache_stats:
                    self._cache_stats[func.__name__] = {"hits": 0, "misses": 0}
                
                # Проверяем кэш
                if key in self._method_cache:
                    cache_entry = self._method_cache[key]
                    created_at = cache_entry["created_at"]
                    
                    # Проверяем TTL
                    age = (datetime.datetime.now() - created_at).total_seconds()
                    if age < ttl:
                        self._cache_stats[func.__name__]["hits"] += 1
                        return cache_entry["result"]
                    else:
                        # Кэш устарел
                        del self._method_cache[key]
                
                # Вычисляем результат
                self._cache_stats[func.__name__]["misses"] += 1
                result = func(*args, **kwargs)
                
                # Сохраняем в кэш
                self._method_cache[key] = {
                    "result": result,
                    "created_at": datetime.datetime.now()
                }
                
                return result
            
            return wrapper
        return decorator
    
    def clear_cache(self, method_name: str = None) -> None:
        """Очистка кэша"""
        if method_name:
            # Очищаем кэш конкретного метода
            keys_to_remove = [k for k in self._method_cache.keys() if k.startswith(method_name)]
            for key in keys_to_remove:
                del self._method_cache[key]
        else:
            # Очищаем весь кэш
            self._method_cache.clear()
    
    def get_cache_stats(self) -> Dict[str, Dict[str, int]]:
        """Статистика кэша"""
        return self._cache_stats.copy()

# Абстрактный базовый класс с использованием миксинов
class Document(ABC, SerializationMixin, LoggingMixin, ValidationMixin, CacheableMixin):
    """Абстрактный класс документа с миксинами"""
    
    def __init__(self, title: str, content: str, author: str):
        # Важно: вызываем конструкторы миксинов
        super().__init__()
        
        self.title = title
        self.content = content
        self.author = author
        self.created_at = datetime.datetime.now()
        self.modified_at = self.created_at
        self.version = 1
        
        # Настройка валидации
        self._setup_validation()
        
        self.log_action("document_initialized", {
            "title": title,
            "author": author,
            "content_length": len(content)
        })
    
    def _setup_validation(self):
        """Настройка правил валидации"""
        self.add_validation_rule(
            "title",
            lambda x: isinstance(x, str) and len(x.strip()) > 0,
            "Заголовок должен быть непустой строкой"
        )
        
        self.add_validation_rule(
            "title",
            lambda x: len(x) <= 200,
            "Заголовок не должен превышать 200 символов"
        )
        
        self.add_validation_rule(
            "author",
            lambda x: isinstance(x, str) and len(x.strip()) > 0,
            "Автор должен быть указан"
        )
        
        self.add_validation_rule(
            "content",
            lambda x: isinstance(x, str),
            "Содержимое должно быть строкой"
        )
    
    # Абстрактные методы
    @abstractmethod
    def get_document_type(self) -> str:
        """Тип документа"""
        pass
    
    @abstractmethod
    def render(self) -> str:
        """Рендеринг документа"""
        pass
    
    @abstractmethod
    def get_word_count(self) -> int:
        """Подсчет слов"""
        pass
    
    # Конкретные методы
    def update_content(self, new_content: str) -> None:
        """Обновление содержимого"""
        old_length = len(self.content)
        self.content = new_content
        self.modified_at = datetime.datetime.now()
        self.version += 1
        
        self.log_action("content_updated", {
            "old_length": old_length,
            "new_length": len(new_content),
            "version": self.version
        })
    
    def update_title(self, new_title: str) -> None:
        """Обновление заголовка"""
        if not self.validate_field("title", new_title):
            raise ValueError("Некорректный заголовок")
        
        old_title = self.title
        self.title = new_title
        self.modified_at = datetime.datetime.now()
        
        self.log_action("title_updated", {
            "old_title": old_title,
            "new_title": new_title
        })
    
    @cached_method(ttl=300)  # Кэшируем на 5 минут
    def get_statistics(self) -> Dict[str, Any]:
        """Статистика документа (кэшируется)"""
        import time
        time.sleep(0.1)  # Имитируем сложные вычисления
        
        word_count = self.get_word_count()
        char_count = len(self.content)
        char_count_no_spaces = len(self.content.replace(' ', ''))
        
        return {
            "word_count": word_count,
            "character_count": char_count,
            "character_count_no_spaces": char_count_no_spaces,
            "estimated_reading_time": max(1, word_count // 200),  # минуты
            "version": self.version,
            "created_at": self.created_at.isoformat(),
            "modified_at": self.modified_at.isoformat()
        }
    
    def to_dict(self) -> Dict[str, Any]:
        """Сериализация в словарь"""
        return {
            "title": self.title,
            "content": self.content,
            "author": self.author,
            "created_at": self.created_at.isoformat(),
            "modified_at": self.modified_at.isoformat(),
            "version": self.version,
            "document_type": self.get_document_type()
        }
    
    @classmethod
    def from_dict(cls, data: Dict[str, Any]):
        """Десериализация из словаря"""
        # Этот метод должен быть переопределен в наследниках
        raise NotImplementedError("Метод должен быть переопределен в наследниках")
    
    def __str__(self):
        return f"{self.get_document_type()}('{self.title}' by {self.author})"

class Article(Document):
    """Статья"""
    
    def __init__(self, title: str, content: str, author: str, category: str = "general"):
        self.category = category
        super().__init__(title, content, author)
        
        # Дополнительные правила валидации для статьи
        self.add_validation_rule(
            "content",
            lambda x: len(x) >= 100,
            "Статья должна содержать минимум 100 символов"
        )
    
    def get_document_type(self) -> str:
        return "Article"
    
    def render(self) -> str:
        stats = self.get_statistics()
        header = f"# {self.title}\n"
        header += f"**Автор:** {self.author} | **Категория:** {self.category}\n"
        header += f"**Слов:** {stats['word_count']} | **Время чтения:** {stats['estimated_reading_time']} мин\n"
        header += "-" * 50 + "\n\n"
        
        return header + self.content
    
    def get_word_count(self) -> int:
        return len(self.content.split())
    
    @classmethod
    def from_dict(cls, data: Dict[str, Any]) -> 'Article':
        article = cls(
            data["title"],
            data["content"], 
            data["author"],
            data.get("category", "general")
        )
        
        # Восстанавливаем временные метки
        article.created_at = datetime.datetime.fromisoformat(data["created_at"])
        article.modified_at = datetime.datetime.fromisoformat(data["modified_at"])
        article.version = data.get("version", 1)
        
        return article

class Report(Document):
    """Отчет"""
    
    def __init__(self, title: str, content: str, author: str, 
                 report_type: str = "general", confidential: bool = False):
        self.report_type = report_type
        self.confidential = confidential
        super().__init__(title, content, author)
        
        # Дополнительные правила валидации
        self.add_validation_rule(
            "report_type",
            lambda x: x in ["general", "financial", "technical", "quarterly", "annual"],
            "Некорректный тип отчета"
        )
    
    def get_document_type(self) -> str:
        return "Report"
    
    def render(self) -> str:
        stats = self.get_statistics()
        confidential_mark = " [КОНФИДЕНЦИАЛЬНО]" if self.confidential else ""
        
        header = f"ОТЧЕТ: {self.title}{confidential_mark}\n"
        header += f"Тип: {self.report_type.upper()}\n"
        header += f"Автор: {self.author}\n"
        header += f"Дата: {self.created_at.strftime('%d.%m.%Y')}\n"
        header += f"Версия: {self.version}\n"
        header += f"Статистика: {stats['word_count']} слов, {stats['character_count']} символов\n"
        header += "=" * 60 + "\n\n"
        
        return header + self.content
    
    def get_word_count(self) -> int:
        # Для отчетов считаем слова без учета служебных символов
        import re
        words = re.findall(r'\b\w+\b', self.content)
        return len(words)
    
    @classmethod
    def from_dict(cls, data: Dict[str, Any]) -> 'Report':
        report = cls(
            data["title"],
            data["content"],
            data["author"],
            data.get("report_type", "general"),
            data.get("confidential", False)
        )
        
        report.created_at = datetime.datetime.fromisoformat(data["created_at"])
        report.modified_at = datetime.datetime.fromisoformat(data["modified_at"])
        report.version = data.get("version", 1)
        
        return report

def demonstrate_mixins_and_multiple_inheritance():
    """Демонстрация миксинов и множественного наследования"""
    
    print("\n=== Миксины и множественное наследование ===")
    
    print("1. Создание документов:")
    
    article = Article(
        "Введение в Python",
        "Python — это высокоуровневый язык программирования общего назначения. " * 10 +
        "Он отличается простотой синтаксиса и мощными возможностями. " * 5 +
        "Python широко используется в веб-разработке, анализе данных, машинном обучении.",
        "Иван Иванов",
        "programming"
    )
    
    report = Report(
        "Квартальный отчет",
        "В этом квартале компания показала отличные результаты. " * 15 +
        "Выручка выросла на 25%, прибыль на 30%. Планы на следующий квартал включают расширение.",
        "Мария Петрова",
        "quarterly",
        confidential=True
    )
    
    documents = [article, report]
    
    for doc in documents:
        print(f"   {doc}")
    
    print("\n2. Тестирование функций миксинов:")
    
    # Логирование
    print("   Логи статьи:")
    for log in article.get_recent_logs(3):
        print(f"     {log['timestamp'][:19]}: {log['action']}")
    
    # Валидация
    print("\n   Валидация:")
    for doc in documents:
        is_valid = doc.validate_all()
        status = "✅" if is_valid else "❌"
        print(f"     {doc.get_document_type()}: {status}")
        if not is_valid:
            for error in doc.validation_errors:
                print(f"       - {error}")
    
    # Кэширование
    print("\n   Тестирование кэширования (статистика):")
    
    import time
    
    # Первый вызов - медленный
    start = time.time()
    stats1 = article.get_statistics()
    time1 = time.time() - start
    
    # Второй вызов - быстрый (из кэша)
    start = time.time()
    stats2 = article.get_statistics()
    time2 = time.time() - start
    
    print(f"     Первый вызов: {time1:.3f}с")
    print(f"     Второй вызов: {time2:.3f}с (из кэша)")
    print(f"     Ускорение: {time1/time2:.1f}x")
    
    # Статистика кэша
    cache_stats = article.get_cache_stats()
    print(f"     Статистика кэша: {cache_stats}")
    
    print("\n3. Сериализация:")
    
    # JSON сериализация
    article_json = article.to_json()
    print(f"   Размер JSON статьи: {len(article_json)} символов")
    
    # Восстановление из JSON
    restored_article = Article.from_json(article_json)
    print(f"   Восстановленная статья: {restored_article}")
    print(f"   Заголовки совпадают: {article.title == restored_article.title}")
    
    print("\n4. Обновление документов:")
    
    article.update_title("Продвинутый Python")
    article.update_content(article.content + "\n\nДополнительная глава о декораторах.")
    
    print(f"   Обновленная статья: версия {article.version}")
    print(f"   Последние действия:")
    
    for log in article.get_recent_logs(3):
        action_desc = {
            "title_updated": "Обновлен заголовок",
            "content_updated": "Обновлено содержимое"
        }.get(log['action'], log['action'])
        print(f"     - {action_desc}")
    
    print("\n5. Рендеринг документов:")
    
    print("   Статья:")
    print(article.render()[:200] + "...")
    
    print("\n   Отчет:")
    print(report.render()[:300] + "...")
    
    print("\n6. MRO (Method Resolution Order):")
    
    print(f"   MRO для Article: {[cls.__name__ for cls in Article.__mro__]}")
    print(f"   MRO для Report: {[cls.__name__ for cls in Report.__mro__]}")
    
    # Демонстрация того, что миксины добавляют функциональность
    print(f"\n   Article наследует от:")
    for base in Article.__bases__:
        print(f"     - {base.__name__}")
    
    print("\n7. Статистика документов:")
    
    for doc in documents:
        stats = doc.get_statistics()
        print(f"\n   {doc.get_document_type()} '{doc.title}':")
        for key, value in stats.items():
            if key in ['created_at', 'modified_at']:
                continue
            print(f"     {key}: {value}")

demonstrate_mixins_and_multiple_inheritance()
```

##### 🔸Обобщение
###### ▫️Сравнение подходов:

| Аспект                 | ABC               | Protocol            | Миксин            |
| ---------------------- | ----------------- | ------------------- | ----------------- |
| **Наследование**       | Обязательное      | Структурное         | Множественное     |
| **Проверка**           | Во время создания | Во время выполнения | Во время создания |
| **Гибкость**           | Средняя           | Высокая             | Высокая           |
| **Производительность** | Хорошая           | Средняя             | Хорошая           |

###### ▫️Лучшие практики:

**✅ Используйте ABC когда:**
- Нужна строгая иерархия наследования
- Требуется обязательная реализация методов
- Хотите предоставить частичную реализацию
- Нужен контроль времени создания объектов

**✅ Используйте Protocols когда:**
- Нужна структурная типизация
- Хотите избежать жесткого наследования
- Интегрируетесь с существующим кодом
- Используете утиную типизацию

**✅ Используйте Миксины когда:**
- Нужна переиспользуемая функциональность
- Хотите комбинировать возможности
- Требуется горизонтальное расширение
- Избегаете дублирования кода

 ###### ▫️Паттерны применения:
 
**Template Method**:
```python
class Algorithm(ABC):
    def process(self):
        self.step1()
        self.step2()
        self.step3()
    
    @abstractmethod
    def step2(self): pass  # Обязательная реализация
    
    def step1(self): pass  # Опциональная
    def step3(self): pass  # Опциональная
```

**Strategy**:
```python
class Strategy(ABC):
    @abstractmethod
    def execute(self, data): pass

class Context:
    def __init__(self, strategy: Strategy):
        self.strategy = strategy
```

**Factory**
```python
class Creator(ABC):
    @abstractmethod
    def create_product(self): pass
```

**Plugin System**
```python
class Plugin(ABC):
    @abstractmethod
    def process(self, data): pass

# Автоматическая регистрация наследников
```

 ###### ▫️Ошибки, которых стоит избегать:
**❌ Не делайте:**
- Слишком глубокие иерархии наследования
- Абстрактные классы без абстрактных методов
- Миксины с состоянием и сложной логикой
- Игнорирование MRO при множественном наследовании

**✅ Делайте:**
- Предпочитайте композицию наследованию
- Используйте type hints для лучшей документации
- Тестируйте все абстрактные методы
- Документируйте контракты и ожидания
---
#### 🔹 Декораторы классов

>Декораторы классов — это мощный механизм для модификации или расширения поведения классов без изменения их исходного кода. Они применяются к классам и могут изменять их структуру, добавлять методы, атрибуты или полностью заменять класс

##### 🔸 Простейшие декораторы классов
```python
from typing import Any, Type, Dict, List, Callable, Optional
import functools
import time
import datetime
from dataclasses import dataclass

def simple_class_decorator(cls: Type) -> Type:
    """Простейший декоратор класса"""
    print(f"🎯 Декорирую класс {cls.__name__}")
    
    # Добавляем атрибут к классу
    cls.decorated = True
    cls.decoration_time = datetime.datetime.now()
    
    # Добавляем метод к классу
    def get_decoration_info(self) -> Dict[str, Any]:
        return {
            "decorated": True,
            "decoration_time": self.__class__.decoration_time.isoformat(),
            "class_name": self.__class__.__name__
        }
    
    cls.get_decoration_info = get_decoration_info
    
    return cls

def add_string_representation(cls: Type) -> Type:
    """Декоратор для автоматического добавления __str__ и __repr__"""
    
    def __str__(self) -> str:
        # Получаем все атрибуты экземпляра
        attrs = []
        for key, value in self.__dict__.items():
            if not key.startswith('_'):
                if isinstance(value, str):
                    attrs.append(f"{key}='{value}'")
                else:
                    attrs.append(f"{key}={value}")
        
        attrs_str = ', '.join(attrs)
        return f"{self.__class__.__name__}({attrs_str})"
    
    def __repr__(self) -> str:
        return self.__str__()
    
    # Добавляем методы только если их нет
    if not hasattr(cls, '__str__') or cls.__str__ is object.__str__:
        cls.__str__ = __str__
    
    if not hasattr(cls, '__repr__') or cls.__repr__ is object.__repr__:
        cls.__repr__ = __repr__
    
    return cls

def singleton(cls: Type) -> Type:
    """Декоратор Singleton - создает только один экземпляр класса"""
    instances: Dict[Type, Any] = {}
    
    @functools.wraps(cls)
    def get_instance(*args, **kwargs):
        if cls not in instances:
            print(f"🔄 Создаем новый экземпляр {cls.__name__}")
            instances[cls] = cls(*args, **kwargs)
        else:
            print(f"♻️  Возвращаем существующий экземпляр {cls.__name__}")
        
        return instances[cls]
    
    # Добавляем метод для получения информации о состоянии singleton
    get_instance.get_instance_info = lambda: {
        "class": cls.__name__,
        "has_instance": cls in instances,
        "instance_id": id(instances[cls]) if cls in instances else None
    }
    
    # Добавляем метод для сброса singleton
    get_instance.reset_instance = lambda: instances.pop(cls, None)
    
    return get_instance

def demonstrate_basic_decorators():
    """Демонстрация базовых декораторов классов"""
    
    print("=== Базовые декораторы классов ===")
    
    print("1. Простой декоратор:")
    
    @simple_class_decorator
    class Person:
        def __init__(self, name: str, age: int):
            self.name = name
            self.age = age
    
    person = Person("Алиса", 30)
    
    print(f"   Класс декорирован: {Person.decorated}")
    print(f"   Время декорации: {Person.decoration_time}")
    print(f"   Информация о декорации: {person.get_decoration_info()}")
    
    print("\n2. Автоматическое строковое представление:")
    
    @add_string_representation
    class Product:
        def __init__(self, name: str, price: float, category: str):
            self.name = name
            self.price = price
            self.category = category
            self.created_at = datetime.datetime.now()
    
    product = Product("Ноутбук", 1500.0, "Электроника")
    
    print(f"   str(product): {str(product)}")
    print(f"   repr(product): {repr(product)}")
    
    print("\n3. Singleton декоратор:")
    
    @singleton
    class DatabaseConnection:
        def __init__(self, host: str = "localhost", port: int = 5432):
            self.host = host
            self.port = port
            self.connected_at = datetime.datetime.now()
            print(f"🔌 Подключение к базе данных {host}:{port}")
        
        def query(self, sql: str) -> str:
            return f"Выполняем запрос: {sql}"
    
    # Создаем несколько "экземпляров"
    db1 = DatabaseConnection()
    db2 = DatabaseConnection("server1", 3306)
    db3 = DatabaseConnection()
    
    print(f"   db1 is db2: {db1 is db2}")
    print(f"   db1 is db3: {db1 is db3}")
    print(f"   ID db1: {id(db1)}")
    print(f"   ID db2: {id(db2)}")
    print(f"   ID db3: {id(db3)}")
    
    # Информация о singleton
    print(f"   Информация: {DatabaseConnection.get_instance_info()}")
    
    # Сброс singleton
    DatabaseConnection.reset_instance()
    db4 = DatabaseConnection("new_host")
    print(f"   После сброса, новый экземпляр: {db4.host}")

demonstrate_basic_decorators()
```

##### 🔸 Декораторы с параметрами
```python
import inspect
import warnings
from typing import Union, Callable

def validate_attributes(**validations: Callable[[Any], bool]):
    """Декоратор для валидации атрибутов класса"""
    
    def decorator(cls: Type) -> Type:
        original_init = cls.__init__
        
        @functools.wraps(original_init)
        def new_init(self, *args, **kwargs):
            # Вызываем оригинальный __init__
            original_init(self, *args, **kwargs)
            
            # Выполняем валидацию
            validation_errors = []
            for attr_name, validator in validations.items():
                if hasattr(self, attr_name):
                    value = getattr(self, attr_name)
                    try:
                        if not validator(value):
                            validation_errors.append(f"Атрибут '{attr_name}' не прошел валидацию")
                    except Exception as e:
                        validation_errors.append(f"Ошибка валидации '{attr_name}': {e}")
                else:
                    validation_errors.append(f"Отсутствует обязательный атрибут '{attr_name}'")
            
            if validation_errors:
                raise ValueError(f"Ошибки валидации: {'; '.join(validation_errors)}")
        
        cls.__init__ = new_init
        cls._validation_rules = validations
        
        return cls
    
    return decorator

def add_methods(**methods: Callable):
    """Декоратор для добавления методов в класс"""
    
    def decorator(cls: Type) -> Type:
        for method_name, method_func in methods.items():
            # Привязываем метод к классу
            setattr(cls, method_name, method_func)
        
        return cls
    
    return decorator

def deprecate_class(reason: str = "", version: str = "", alternative: str = ""):
    """Декоратор для пометки класса как устаревшего"""
    
    def decorator(cls: Type) -> Type:
        original_init = cls.__init__
        
        @functools.wraps(original_init)
        def new_init(self, *args, **kwargs):
            message = f"Класс {cls.__name__} устарел"
            if version:
                message += f" с версии {version}"
            if reason:
                message += f". Причина: {reason}"
            if alternative:
                message += f". Используйте {alternative}"
            
            warnings.warn(message, DeprecationWarning, stacklevel=2)
            original_init(self, *args, **kwargs)
        
        cls.__init__ = new_init
        cls.__deprecated__ = True
        cls.__deprecation_info__ = {
            "reason": reason,
            "version": version,
            "alternative": alternative
        }
        
        return cls
    
    return decorator

def auto_property(*attributes: str, readonly: bool = False):
    """Автоматическое создание свойств для атрибутов"""
    
    def decorator(cls: Type) -> Type:
        for attr in attributes:
            private_attr = f"_{attr}"
            
            def make_getter(private_name: str, public_name: str):
                def getter(self) -> Any:
                    if not hasattr(self, private_name):
                        raise AttributeError(f"Атрибут '{public_name}' не инициализирован")
                    return getattr(self, private_name)
                return getter
            
            def make_setter(private_name: str, public_name: str):
                def setter(self, value: Any) -> None:
                    old_value = getattr(self, private_name, None)
                    setattr(self, private_name, value)
                    
                    # Вызываем callback если он есть
                    callback_name = f"_on_{public_name}_changed"
                    if hasattr(self, callback_name):
                        callback = getattr(self, callback_name)
                        callback(old_value, value)
                
                return setter
            
            # Создаем property
            if readonly:
                prop = property(make_getter(private_attr, attr))
            else:
                prop = property(
                    make_getter(private_attr, attr),
                    make_setter(private_attr, attr)
                )
            
            setattr(cls, attr, prop)
        
        return cls
    
    return decorator

def cache_methods(*method_names: str, ttl: int = 3600):
    """Декоратор для кэширования результатов методов класса"""
    
    def decorator(cls: Type) -> Type:
        # Добавляем атрибут для хранения кэша
        cls._method_cache = {}
        cls._cache_ttl = ttl
        
        for method_name in method_names:
            if hasattr(cls, method_name):
                original_method = getattr(cls, method_name)
                
                @functools.wraps(original_method)
                def cached_method(self, *args, method=original_method, name=method_name, **kwargs):
                    # Генерируем ключ кэша
                    cache_key = f"{name}_{hash(str(args) + str(sorted(kwargs.items())))}"
                    
                    # Проверяем кэш
                    if cache_key in self._method_cache:
                        cached_result, cached_time = self._method_cache[cache_key]
                        
                        # Проверяем TTL
                        if time.time() - cached_time < self._cache_ttl:
                            return cached_result
                        else:
                            # Кэш устарел
                            del self._method_cache[cache_key]
                    
                    # Вычисляем результат
                    result = method(self, *args, **kwargs)
                    
                    # Сохраняем в кэш
                    self._method_cache[cache_key] = (result, time.time())
                    
                    return result
                
                setattr(cls, method_name, cached_method)
        
        # Добавляем метод для очистки кэша
        def clear_cache(self, method_name: str = None) -> None:
            if method_name:
                keys_to_remove = [k for k in self._method_cache.keys() if k.startswith(method_name)]
                for key in keys_to_remove:
                    del self._method_cache[key]
            else:
                self._method_cache.clear()
        
        cls.clear_cache = clear_cache
        
        return cls
    
    return decorator

def demonstrate_parameterized_decorators():
    """Демонстрация декораторов с параметрами"""
    
    print("\n=== Декораторы с параметрами ===")
    
    print("1. Валидация атрибутов:")
    
    @validate_attributes(
        name=lambda x: isinstance(x, str) and len(x) > 0,
        age=lambda x: isinstance(x, int) and 0 <= x <= 150,
        email=lambda x: isinstance(x, str) and "@" in x
    )
    class User:
        def __init__(self, name: str, age: int, email: str):
            self.name = name
            self.age = age
            self.email = email
    
    # Валидные данные
    try:
        user1 = User("Иван", 25, "ivan@example.com")
        print(f"   ✅ Создан пользователь: {user1.name}")
    except ValueError as e:
        print(f"   ❌ Ошибка: {e}")
    
    # Невалидные данные
    try:
        user2 = User("", -5, "invalid-email")
    except ValueError as e:
        print(f"   ❌ Ошибка валидации: {e}")
    
    print("\n2. Добавление методов:")
    
    def calculate_discount(self, percentage: float) -> float:
        """Расчет скидки"""
        return self.price * (percentage / 100)
    
    def format_price(self) -> str:
        """Форматирование цены"""
        return f"${self.price:.2f}"
    
    @add_methods(
        calculate_discount=calculate_discount,
        format_price=format_price
    )
    class Product:
        def __init__(self, name: str, price: float):
            self.name = name
            self.price = price
    
    product = Product("Телефон", 999.99)
    print(f"   Товар: {product.name}")
    print(f"   Цена: {product.format_price()}")
    print(f"   Скидка 10%: ${product.calculate_discount(10):.2f}")
    
    print("\n3. Устаревший класс:")
    
    @deprecate_class(
        reason="Использует устаревший API",
        version="2.0",
        alternative="NewCalculator"
    )
    class OldCalculator:
        def __init__(self):
            pass
        
        def add(self, a: float, b: float) -> float:
            return a + b
    
    print("   Создаем экземпляр устаревшего класса...")
    with warnings.catch_warnings(record=True) as w:
        warnings.simplefilter("always")
        calc = OldCalculator()
        if w:
            print(f"   ⚠️  {w[0].message}")
    
    print("\n4. Автоматические свойства:")
    
    @auto_property("temperature", "humidity", readonly=False)
    class WeatherStation:
        def __init__(self, location: str):
            self.location = location
            self._temperature = 20.0
            self._humidity = 50.0
        
        def _on_temperature_changed(self, old_value: float, new_value: float):
            print(f"       🌡️  Температура изменилась: {old_value}°C → {new_value}°C")
        
        def _on_humidity_changed(self, old_value: float, new_value: float):
            print(f"       💧 Влажность изменилась: {old_value}% → {new_value}%")
    
    station = WeatherStation("Москва")
    print(f"   Станция: {station.location}")
    print(f"   Текущая температура: {station.temperature}°C")
    
    # Изменяем значения через свойства
    station.temperature = 25.5
    station.humidity = 65.0
    
    print(f"   Новые показания: {station.temperature}°C, {station.humidity}%")
    
    print("\n5. Кэширование методов:")
    
    @cache_methods("expensive_calculation", "fetch_data", ttl=2)
    class DataProcessor:
        def __init__(self, name: str):
            self.name = name
            self.calculation_count = 0
            self.fetch_count = 0
        
        def expensive_calculation(self, n: int) -> int:
            """Дорогие вычисления"""
            print(f"       💻 Выполняем сложные вычисления для n={n}")
            self.calculation_count += 1
            time.sleep(0.1)  # Имитируем задержку
            return sum(range(n))
        
        def fetch_data(self, source: str) -> str:
            """Получение данных"""
            print(f"       📡 Получаем данные из {source}")
            self.fetch_count += 1
            time.sleep(0.05)  # Имитируем задержку
            return f"Данные из {source} в {datetime.datetime.now()}"
    
    processor = DataProcessor("TestProcessor")
    
    print("   Первые вызовы (медленные):")
    start = time.time()
    result1 = processor.expensive_calculation(1000)
    time1 = time.time() - start
    
    start = time.time()
    data1 = processor.fetch_data("API")
    time2 = time.time() - start
    
    print(f"     Результат вычислений: {result1}")
    print(f"     Время вычислений: {time1:.3f}с")
    print(f"     Время получения данных: {time2:.3f}с")
    
    print("\n   Повторные вызовы (быстрые, из кэша):")
    start = time.time()
    result2 = processor.expensive_calculation(1000)
    time3 = time.time() - start
    
    start = time.time()
    data2 = processor.fetch_data("API")
    time4 = time.time() - start
    
    print(f"     Ускорение вычислений: {time1/time3:.1f}x")
    print(f"     Ускорение получения данных: {time2/time4:.1f}x")
    print(f"     Счетчик вычислений: {processor.calculation_count}")
    print(f"     Счетчик запросов: {processor.fetch_count}")
    
    # Ждем истечения TTL
    print("\n   Ждем истечения TTL (2 сек)...")
    time.sleep(2.1)
    
    print("   Вызов после истечения TTL:")
    result3 = processor.expensive_calculation(1000)
    print(f"     Счетчик после истечения TTL: {processor.calculation_count}")

demonstrate_parameterized_decorators()
```

##### 🔸 Основные типы декораторов классов

**Простые декораторы**
```python
def simple_decorator(cls):
    # Модифицируем класс
    cls.new_attribute = "value"
    return cls

@simple_decorator
class MyClass:
    pass
```

**Параметрические декораторы**
```python
def parametric_decorator(param1, param2):
    def decorator(cls):
        cls.param1 = param1
        cls.param2 = param2
        return cls
    return decorator

@parametric_decorator("value1", "value2")
class MyClass:
    pass
```

**Декораторы-классы**
```python
class DecoratorClass:
    def __init__(self, param):
        self.param = param
    
    def __call__(self, cls):
        cls.param = self.param
        return cls

@DecoratorClass("value")
class MyClass:
    pass
```

##### 🔸 Возможности декораторов классов:

|Возможность|Описание|Применение|
|---|---|---|
|**Добавление атрибутов**|Присоединение новых свойств|Конфигурация, метаданные|
|**Добавление методов**|Внедрение новой функциональности|Миксины, расширения API|
|**Модификация методов**|Обертывание существующих методов|Логирование, кэширование, валидация|
|**Изменение наследования**|Модификация MRO|Добавление базовых классов|
|**Регистрация классов**|Автоматическая регистрация|Плагины, фабрики|
|**Валидация**|Проверка структуры класса|Контракты, типизация|

---
#### 🔹 Прокси

>Иногда класс реализует обертку вокруг другого объекта для создания объекта-заместителя. Прокси — это объект, предоставляющий тот же интерфейс, что и другой объект, но по какой-то причине не связанный с исходным объектом наследованием. Этим он отличается от композиции, где создается совершенно новый объект с другими объектами, но при этом обладающий своим уникальным набором методов и атрибутов. Прокси могут встречаться во многих реальных сценариях. Например, в распределенных вычислениях фактическая реализация объекта может быть на удаленном сервере в облаке. Клиенты, взаимодействующие с ним, могут использовать прокси, который выглядит как объект на сервере, но при этом незаметно делегирует все свои вызовы методов с помощью сетевых сообщений.

==Типичная реализация заместителей основана на методе __getattr__():==
```python
class A:
	def spam(self):
		print('A.spam')

	def grok(self):
		print('A.grok')

	def yow(self):
		print('A.yow')

class LoggedA:

	def __init__(self):
		self._a = A()

	def __getattr__(self, name):
		print("Accessing", name)
		# Делегирование внутреннему экземпляру A
		return getattr(self._a, name)

# Пример использования
a = LoggedA()
a.spam() # Выводит "Accessing spam" и "A.spam"
a.yow() # Выводит "Accessing yow" и "A.yow"
```

##### 🔸 Делегирование иногда используется как альтернатива наследованию
```python
class A:
	def spam(self):
		print('A.spam')

	def grok(self):
		print('A.grok')

	def yow(self):
		print('A.yow')

class B:
	def __init__(self):
		self._a = A()

	def grok(self):
		print('B.grok')
		
	def __getattr__(self, name):
		return getattr(self._a, name)
		
# Пример использования
b = B()

b.spam() # -> A.spam
b.grok() # -> B.grok (переопределенный метод)
b.yow() # -> A.yow
```
---
#### 🔹 Сокращение затрат памяти используя `__slots__` 

Как вы уже знаете, экземпляры хранят свои данные в словаре. Создание большого числа экземпляров может привести к значительным затратам памяти. Если имена атрибутов фиксированы, их можно задать в специальной переменной класса `__slots__`:
```python
class Account(object):
	__slots__ = ('owner', 'balance')
	...
```

`__slots__` может рассматриваться как аннотация определения, позволяющая Python провести оптимизации как по затратам памяти, так и по скорости выполнения. Экземпляры класса со __slots__ не используют словарь для хранения данных экземпляров. Вместо этого применяется гораздо более компактная структура данных на базе массива. В программах, создающих множество объектов, использование __slots__ поможет сократить затраты памяти и улучшить скорость выполнения.

>⚠️ Учтите, что __slots__ сложно взаимодействует с наследованием, он несовместима с множественным наследованием. Также использование `__slots__` может нарушить работу кода, предполагающего, что экземпляры содержат атрибут `__dict__`, т.к. его уже нет при использовании `__slots__`
##### 🔸 Пример по экономии памяти
```python
import sys  
import time  
import psutil  
import os  
  
  
def get_memory_usage() -> float:  
    """Получение текущего использования памяти в MB"""  
    process = psutil.Process(os.getpid())  
    return process.memory_info().rss / 1024 / 1024  
  
  
class RegularPoint:  
    """Обычный класс без __slots__"""  
  
    def __init__(self, x: float, y: float, z: float = 0.0):  
        self.x = x  
        self.y = y  
        self.z = z  
        self.created_at = time.time()  
  
    def distance_to_origin(self) -> float:  
        return (self.x ** 2 + self.y ** 2 + self.z ** 2) ** 0.5  
  
    def __str__(self) -> str:  
        return f"Point({self.x}, {self.y}, {self.z})"  
  
  
class SlottedPoint:  
    """Класс с __slots__ для экономии памяти"""  
  
    __slots__ = ('x', 'y', 'z', 'created_at')  
  
    def __init__(self, x: float, y: float, z: float = 0.0):  
        self.x = x  
        self.y = y  
        self.z = z  
        self.created_at = time.time()  
  
    def distance_to_origin(self) -> float:  
        return (self.x ** 2 + self.y ** 2 + self.z ** 2) ** 0.5  
  
    def __str__(self) -> str:  
        return f"SlottedPoint({self.x}, {self.y}, {self.z})"  
  
  
def demonstrate_memory_basics():  
    """Демонстрация основ использования памяти"""  
  
    print("=== Основы использования памяти ===")  
  
    print("1. Размер объектов:")  
  
    regular_point = RegularPoint(1.0, 2.0, 3.0)  
    slotted_point = SlottedPoint(1.0, 2.0, 3.0)  
  
    print(f"   Размер RegularPoint: {sys.getsizeof(regular_point)} байт")  
    print(f"   Размер SlottedPoint: {sys.getsizeof(slotted_point)} байт")  
  
    # Размер __dict__ у обычного объекта  
    if hasattr(regular_point, '__dict__'):  
        print(f"   Размер __dict__ у RegularPoint: {sys.getsizeof(regular_point.__dict__)} байт")  
  
    # У slotted объекта нет __dict__  
    print(f"   Есть ли __dict__ у SlottedPoint: {hasattr(slotted_point, '__dict__')}")  
  
    print("\n2. Структура объектов:")  
  
    print("   Атрибуты RegularPoint:")  
    print(f"     __dict__: {regular_point.__dict__}")  
    print(f"     dir(): {[attr for attr in dir(regular_point) if not attr.startswith('__')]}")  
  
    print("   Атрибуты SlottedPoint:")  
    print(f"     __slots__: {slotted_point.__slots__}")  
  
    # Попытка добавить новый атрибут  
    print("\n3. Гибкость vs ограничения:")  
  
    try:  
        regular_point.new_attribute = "Можно добавить"  
        print(f"   ✅ RegularPoint.new_attribute = {regular_point.new_attribute}")  
    except Exception as e:  
        print(f"   ❌ Не удалось добавить атрибут: {e}")  
  
    try:  
        slotted_point.new_attribute = "Нельзя добавить"  
        print(f"   ✅ SlottedPoint.new_attribute = {slotted_point.new_attribute}")  
    except AttributeError as e:  
        print(f"   ❌ SlottedPoint: {e}")  
  
    print("\n4. Производительность доступа:")  
  
    # Измеряем время доступа к атрибутам  
    iterations = 1000000  
  
    # Regular Point  
    start_time = time.time()  
    for _ in range(iterations):  
        _ = regular_point.x + regular_point.y + regular_point.z  
    regular_time = time.time() - start_time  
  
    # Slotted Point  
    start_time = time.time()  
    for _ in range(iterations):  
        _ = slotted_point.x + slotted_point.y + slotted_point.z  
    slotted_time = time.time() - start_time  
  
    print(f"   Время доступа к атрибутам (1M итераций):")  
    print(f"     RegularPoint: {regular_time:.4f}с")  
    print(f"     SlottedPoint: {slotted_time:.4f}с")  
    print(f"     Ускорение: {regular_time / slotted_time:.2f}x")  
  
  
demonstrate_memory_basics()
```
---