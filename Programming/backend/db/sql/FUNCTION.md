#database #sql

### 🔹 Функции агрегирования
Такая операция существенно отличается от выбора поля тем, что 
выходные данные содержат единственное значение независимо 
от кол-ва строк в таблице. По этой причине агрегатные 
функции и поля не могут выбираться одновременно, если только 
не используется инструкция `GROUP ВУ`. Нельзя применять агрегатную функцию
с агрегатом в качестве аргумента

###### 🔸COUNT
*вычисляет кол-во строк или значений поля, выбранных посредством запроса ,имеет атрибут `DISTINCT` - используется для подсчета числа различных значений данного поля*
```sql
SELECT COUNT (DISTINCT snum) FROM orders;
```

###### 🔸SUM
*вычисляет сумму всех выбранных значений данного поля*
```sql
SELECT SUM(amount) AS total FROM orders;
```

###### 🔸AVG
*вычисляет сумму всех выбранных значений данного поля*
```sql
SELECT AVG(age) FROM employees;
```

###### 🔸MIN / MAX
*вычисляет наименьшее/наибольшее из всех выбранных значений данного поля*
```sql
SELECT MAX(salary), MIN(salary) FROM employees;
```

###### 🔸 GROUP_CONCAT (MySQL) / STRING_AGG (PostgreSQL)
*объединяет значения из группы*
```sql
-- MySQL  
SELECT GROUP_CONCAT(name SEPARATOR ', ') FROM products;  
  
-- PostgreSQL  
SELECT STRING_AGG(name, ', ') FROM products;
```

---
### 🔹 Строковые функции
строковые функции в sql возвращают преобразованные строки,они не меняют строки в бд, а работают только на вывод

###### 🔸 CONCAT
*объединяет строки*
```sql
-- MySQL  
SELECT CONCAT(first_name, ' ', last_name) AS full_name 
FROM users; 
  
-- PostgreSQL  
SELECT first_name || ' ' || last_name AS full_name 
FROM users;
```

###### 🔸 SUBSTRING
*извлекает подстроку*
```sql
-- MySQL  
SELECT SUBSTRING(description, 1, 100) FROM articles; 
  
-- PostgreSQL  
SELECT SUBSTRING(description FROM 1 FOR 100) FROM articles;
```

###### 🔸 RIGHT / LEFT
*Функция LEFT/RIGHT извлекает указанное количество символов с начала/конца строки.*
```sql
LEFT(строка, количество_символов)
  
SELECT LEFT('Hello World', 5); -- Вернет 'Hello'

SELECT LEFT(phone_number, 3) AS area_code FROM clients;

-- если в бд номер с кодом +1, то вернет список без этого кода
SELECT RIGHT(customer_number, LENGTH(customer_number) - 2)  
FROM calls  
WHERE LEFT(customer_number, 2) = '+1';
```

###### 🔸 UPPER / LOWER
*Функция UPPER/LOWER преобразует все символы строки в верхний/нижний регистр.*
```sql
SELECT UPPER('Hello World'); -- Вернет 'HELLO WORLD'

-- Хранение email в нижнем регистре для обеспечения уникальности  
INSERT INTO users (email) VALUES (LOWER('User@Example.com'));
```

###### 🔸 REPLACE
*заменяет все вхождения одной подстроки на другую*
```sql
REPLACE(строка, что_заменить, на_что_заменить)

SELECT REPLACE('Hello World', 'World', 'MySQL'); -- Вернет 'Hello MySQL'
  
-- можно использовать вложения  
-- Удаление HTML-тегов (простой пример)  
SELECT REPLACE(REPLACE(content, '<p>', ''), '</p>', '') 
FROM articles;

-- Обновление устаревших доменов email  
UPDATE users   
SET email = REPLACE(email, 'old-domain.com', 'new-domain.com')  
WHERE email LIKE '%@old-domain.com';
```

---
### 🔹 Функции для работы с датами

###### 🔸 NOW / CURRENT_TIMESTAMP
*текущая дата и время*
```sql
SELECT NOW();
```

###### 🔸 DATE_FORMAT (MySQL) / TO_CHAR (PostgreSQL)
*форматирует дату*
```sql
-- MySQL  
SELECT DATE_FORMAT(created_at, '%d.%m.%Y') FROM orders; 
  
-- PostgreSQL  
SELECT TO_CHAR(created_at, 'DD.MM.YYYY') FROM orders;
```

###### 🔸 DATEDIFF (MySQL) / AGE (PostgreSQL)
*разница между датами*
```sql
-- MySQL  
SELECT DATEDIFF(end_date, start_date) FROM projects;
  
-- PostgreSQL  
SELECT AGE(end_date, start_date) FROM projects;
```

---
### 🔹 Математические функции

###### 🔸 ROUND
*округляет число*
```sql
SELECT ROUND(price, 2) FROM products;
```

###### 🔸 CEILING / CEIL
*округляет вверх*
```sql
SELECT CEIL(4.3); -- Вернет 5
```

###### 🔸 FLOOR
*округляет вниз*
```sql
SELECT FLOOR(4.7); -- Вернет 4
```

---
### 🔹 Регулярные выражения

```sql
-- MySQL  
SELECT * FROM products WHERE name REGEXP '^[A-Z]'; 
  
-- PostgreSQL  
SELECT * FROM products WHERE name ~ '^[A-Z]';
```

---