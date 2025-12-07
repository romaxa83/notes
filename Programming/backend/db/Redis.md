#database

### 🔹 Install
###### 🔸 Linux
```bash
// установка сервера
sudo apt-get install redis-server
redis-server -v

// установка клиента
sudo apt install redis-tools
redis-cli -v

// расширение для php
sudo apt-get install php-redis
```

> конфиг находится
> /etc/redis/redis.conf

---
### 🔹 Команды в консоли

🔸 Удаленное подключение
```bash
redis-cli -h host -p port -a password
```

🔸 Аутентифицироваться
```bash
redis-cli
AUTH password
```

🔸 проверка работы,(ответ должен быть PONG)
```bash
PING
```

🔸 списка БД(ключей)
```bash
info keyspace
```

🔸 переход в бд с ключом 6
```bash
select 6
```

🔸 получение всех данных
```bash
keys *
```

🔸 удалить все данные
```bash
flushall
```

🔸 просмотреть все ключи
```bash
redis-cli --scan --pattern '*'
// или
redis-cli KEYS '*'
```

---