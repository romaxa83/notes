#go #migration #database 

- Инфа по стандартом пакете для работы с бд - http://go-database-sql.org/

### 🔹 Postgres

для работы с postgres, нужно установить пакет 
 - github.com/lib/pq

---
### 🔹 Пакет для миграций

- https://github.com/golang-migrate/migrate

==Установка пакет как CLI==
```bash
# скачиваем файл
https://github.com/mattes/migrate/releases/migrate.linux-amd64.tar.gz

# распаковавем
tar -xvzf migrate.linux-amd64.tar.gz

# можем запустить
./migrate.linux-amd64

# чтоб можно было использовать везде как migrate делаем исполняемым
sudo chmod +x migrate.linux-amd64

# копируем в папку bin
sudo cp migrate.linux-amd64 /usr/local/bin/
sudo ln /usr/local/bin/migrate.linux-amd64 /usr/local/bin/migrate
export PATH = $PATH:/usr/local/bin
migrate

# создает файлы для миграций, в папке migrations 
migrate create -ext sql -dir migrations create_user

# запустить миграции
migrate -path migrations -database "postgres://root:root@localhost:5432/restapi_dev?sslmode=disable" up

# зайти для проверки
psql -d restapi_dev -h localhost -U root -W root
```
---