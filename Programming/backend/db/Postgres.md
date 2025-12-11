#database

### 🔹 Install `psql`

```bash
# Обновите Homebrew  
brew update  
  
# Установите PostgreSQL  
brew install postgresql@15  
  
# Или для последней версии  
brew install postgresql

# Запустить PostgreSQL сейчас  
brew services start postgresql@15  
  
# Или для автозапуска при старте системы  
brew services start postgresql@15

# Проверить версию PostgreSQL  
psql --version  
  
# Подключиться к базе данных  
psql -h localhost -U myuser -d myapp_db  
  
# Показать список баз данных  
psql -l

# Остановить PostgreSQL  
brew services stop postgresql@15  
  
# Перезапустить PostgreSQL  
brew services restart postgresql@15  
  
# Проверить статус  
brew services list | grep postgresql
```

### 🔹 Команды в консоли

🔸подключение к бд
```bash
psql -d demo -U postgres
```
> `-d` - название бд
> `-U` - имя пользователя

🔸справка по всем сервисам
```sql
\?
```

🔸просмотреть список всех таблиц
```sql
\dt
```

🔸просмотр всех индексов
```sql
\di
```

🔸просмотр структуры таблицы (blog)
```sql
\d blog
```

🔸список всех sql-команд
```sql
\h
```

🔸вывод описания sql-команд (CREATE TABLE)
```sql
\h CREATE TABLE
```
---
### 🔹 Полнотекстовый поиск

🔸статьи
https://eax.me/postgresql-full-text-search/
https://www.postgresql.org/docs/11/textsearch-tables.html
https://www.postgresql.org/docs/9.5/textsearch-controls.html

---
### 🔹 POSTGIS

https://eax.me/postgis/

---
