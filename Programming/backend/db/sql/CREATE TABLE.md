#database #sql #postgres #mysql
### 🔹Базовый синтаксис CREATE TABLE

```sql
CREATE TABLE имя_таблицы (
	имя_столбца1 тип_данных [ограничения],
	имя_столбца2 тип_данных [ограничения],
	...
	[ограничения_таблицы]
);
```

---
### 🔹Основные типы данных

#### ==Числовые типы==

###### 🔸MySQL:
- **INT** - целое число (-2,147,483,648 до 2,147,483,647)
- **SMALLINT** - малое целое число (-32,768 до 32,767)
- **TINYINT** - очень малое целое число (-128 до 127 или 0 до 255 без знака)
- **MEDIUMINT** - среднее целое число
- **BIGINT** - большое целое число
- **DECIMAL(M,D)** - десятичное число с фиксированной точностью
- **FLOAT** - число с плавающей точкой одинарной точности
- **DOUBLE** - число с плавающей точкой двойной точности

###### 🔸 PostgreSQL:
- **INTEGER** или **INT** - целое число
- **SMALLINT** - малое целое число
- **BIGINT** - большое целое число
- **NUMERIC(M,D)** или **DECIMAL(M,D)** - число с фиксированной точностью
- **REAL** - число с плавающей точкой одинарной точности
- **DOUBLE PRECISION** - число с плавающей точкой двойной точности
- **SERIAL** - автоинкрементное целое число

#### ==Строковые типы==

###### 🔸 MySQL:
- **CHAR(N)** - строка фиксированной длины (до 255 символов)
- **VARCHAR(N)** - строка переменной длины (до 65,535 символов)
- **TINYTEXT** - очень короткий текст (до 255 символов)
- **TEXT** - текст (до 65,535 символов)
- **MEDIUMTEXT** - средний текст (до 16,777,215 символов)
- **LONGTEXT** - длинный текст (до 4,294,967,295 символов)

###### 🔸 PostgreSQL:
- **CHAR(N)** - строка фиксированной длины
- **VARCHAR(N)** - строка переменной длины
- **TEXT** - текст неограниченной длины

#### ==Типы даты и времени==

###### 🔸 MySQL:
- **DATE** - дата (YYYY-MM-DD)
- **TIME** - время (HH:MM:SS)
- **DATETIME** - дата и время (YYYY-MM-DD HH:MM:SS)
- **TIMESTAMP** - временная метка (с 1970-01-01 00:00:00 UTC)
- **YEAR** - год (в формате YYYY)

###### 🔸 PostgreSQL:
- **DATE** - дата
- **TIME** - время
- **TIMESTAMP** - дата и время
- **TIMESTAMPTZ** - дата и время с часовым поясом
- **INTERVAL** - интервал времени

#### ==Бинарные типы==

###### 🔸 MySQL:
- **BINARY(N)** - бинарная строка фиксированной длины
- **VARBINARY(N)** - бинарная строка переменной длины
- **TINYBLOB** - очень маленький бинарный объект
- **BLOB** - бинарный объект
- **MEDIUMBLOB** - средний бинарный объект
- **LONGBLOB** - большой бинарный объект

###### 🔸 PostgreSQL:
- **BYTEA** - бинарные данные

#### ==Специальные типы==

###### 🔸 MySQL:
- **ENUM** - перечисление
- **SET** - набор возможных значений
- **JSON** - JSON-данные

###### 🔸 PostgreSQL:
- **JSON** и **JSONB** - JSON-данные
- **ARRAY** - массив
- **UUID** - универсальный уникальный идентификатор
- **CIDR**, **INET** - сетевые адреса
- **MACADDR** - MAC-адрес
- **GEOMETRY** - геометрические данные

---
### 🔹Ограничения столбцов и таблиц

###### 🔸 Ограничения столбцов

**NOT NULL** - запрещает `NULL` значения
```sql
username VARCHAR(50) NOT NULL
```

**DEFAULT** - устанавливает значение по умолчанию
```sql
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
```

**PRIMARY KEY** - определяет первичный ключ
```sql
id INT PRIMARY KEY
```

**AUTO_INCREMENT** (MySQL) / **SERIAL** (PostgreSQL) - автоинкрементное поле
```sql
-- MySQL
id INT AUTO_INCREMENT PRIMARY KEY

-- PostgreSQL
id SERIAL PRIMARY KEY
```

**UNIQUE** - гарантирует уникальность значений
```sql
email VARCHAR(100) UNIQUE
```

**CHECK** - определяет условие для значений
```sql
age INT CHECK (age >= 18)
```

**REFERENCES** - внешний ключ
```sql
user_id INT REFERENCES users(id)
```

###### 🔸Ограничения таблиц

**PRIMARY KEY** - составной первичный ключ
```sql
PRIMARY KEY (column1, column2)
```

**FOREIGN KEY** - внешний ключ с дополнительными опциями
```sql
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE ON UPDATE CASCADE
```

**UNIQUE** - составное ограничение уникальности
```sql
UNIQUE (email, tenant_id)
```

**CHECK** - проверка на уровне таблицы
```sql
CHECK (start_date < end_date)
```

---
### 🔹Дополнительные параметры и атрибуты

####  ==MySQL==

**ENGINE** - тип хранилища (InnoDB, MyISAM и т.д.)
```sql
CREATE TABLE users (...) ENGINE=InnoDB;
```
> `InnoDB` — одна из выбираемых подсистем низкого уровня в  **MySQL**.
> Основным отличием `InnoDB` от других подсистем низкого уровня **MySQL** 
является наличие механизма транзакций и внешних ключей.

> `InnoDB` и `MYISAM`, являются механизмами хранения для **MySQL**.
Эти два варианта отличаются от реализации блокировки: 
`InnoDB` блокирует определенную строку в таблице, 
а `MYISAM` блокирует всю таблицу **MySQL**.
Вы можете указать тип, указав `MYISAM` OR `InnoDB` при создании таблицы в БД.

**CHARACTER SET** - набор символов
```sql
CREATE TABLE messages (...) CHARACTER SET utf8mb4;
```

**COLLATE** - правила сравнения
```sql
CREATE TABLE users (...) COLLATE utf8mb4_unicode_ci;
```

**COMMENT** - комментарий к таблице
```sql
CREATE TABLE logs (...) COMMENT='Системные логи';
```

#### ==PostgreSQL==

**TABLESPACE** - расположение таблицы
```sql
CREATE TABLE large_data (...) TABLESPACE large_space;
```

**INHERITS** - наследование таблиц
```sql
  CREATE TABLE employees (...) INHERITS (persons);
```

**WITH** - параметры хранения
```sql
CREATE TABLE logs (...) WITH (fillfactor=70);
```

---
### 🔹Модификаторы для индексов

**CREATE INDEX** - создание индекса
```sql
CREATE INDEX idx_username ON users(username);
```

**UNIQUE INDEX** - уникальный индекс
```sql
CREATE UNIQUE INDEX idx_email ON users(email);
```

**FULLTEXT INDEX** (MySQL) - полнотекстовый индекс
```sql
CREATE FULLTEXT INDEX idx_content ON articles(content);
```

**B-TREE/HASH/GIN/GIST** (PostgreSQL) - различные типы индексов
```sql
CREATE INDEX idx_jsondata ON data USING GIN (metadata);
```

---
### 🔹 Пример полного CREATE TABLE

#### 🔸MySQL

```sql
CREATE TABLE orders (
	id INT AUTO_INCREMENT PRIMARY KEY,
	customer_id INT NOT NULL,
	order_date DATETIME DEFAULT CURRENT_TIMESTAMP,
	total_amount DECIMAL(10,2) NOT NULL DEFAULT 0.00,
	status ENUM('pending', 'processing', 'shipped', 'delivered', 'cancelled') DEFAULT 'pending',
	notes TEXT,
	is_paid TINYINT(1) DEFAULT 0,
	created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
	updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
	FOREIGN KEY (customer_id) REFERENCES customers(id) ON DELETE CASCADE,
	INDEX idx_order_date (order_date),
	UNIQUE INDEX idx_order_reference (order_reference)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='Таблица заказов';
```

#### 🔸PostgreSQL

```sql
CREATE TABLE orders (
	id SERIAL PRIMARY KEY,
	customer_id INTEGER NOT NULL,
	order_date TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
	total_amount NUMERIC(10,2) NOT NULL DEFAULT 0.00,
	status TEXT CHECK (status IN ('pending', 'processing', 'shipped', 'delivered', 'cancelled')) DEFAULT 'pending',
	notes TEXT,
	is_paid BOOLEAN DEFAULT FALSE,
	created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
	updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
	CONSTRAINT fk_customer FOREIGN KEY (customer_id) REFERENCES customers(id) ON DELETE CASCADE,
	CONSTRAINT check_total CHECK (total_amount >= 0)
);
CREATE INDEX idx_order_date ON orders(order_date);
CREATE UNIQUE INDEX idx_order_reference ON orders(order_reference);
```

---
### 🔹 Дополнительные опции для внешних ключей

🔸 **ON DELETE CASCADE** - удаляет зависимые записи при удалении родительской
🔸 **ON DELETE SET NULL** - устанавливает NULL при удалении родительской записи
🔸 **ON DELETE RESTRICT** - запрещает удаление родительской записи, если есть зависимые
🔸 **ON DELETE NO ACTION** - аналогично RESTRICT (в большинстве СУБД)
🔸 **ON UPDATE CASCADE** - обновляет зависимые записи при обновлении родительской

Такие же опции доступны и для операции UPDATE.

---