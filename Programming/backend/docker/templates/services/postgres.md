#docker #template #db #postgres 

### 🔹 Сервис для docker-compose

```yml
version: '3'
services:
  db:
    image: postgres:12.1-alpine
    container_name: postgres_db
    hostname: postgres_db
    restart: always
    environment:
      POSTGRES_USER: root
      POSTGRES_PASSWORD: root
      POSTGRES_DB: db
    ports:
      - 192.168.141.1:5432:5432
    volumes:
      - ./database:/var/lib/postgresql/data

  pgadmin:
    image: dpage/pgadmin4
    depends_on:
      - db
    ports:
      - 192.168.141.1:8084:80
    environment:
      PGADMIN_DEFAULT_EMAIL: pgadmin4@pgadmin.org
      PGADMIN_DEFAULT_PASSWORD: admin
    restart: unless-stopped

networks:
  default:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.141.0/24
```

Вход в pgadmin

login - pgadmin4@pgadmin.org
password - admin

подключиться к бд

- выбираем новый сервер
- общие вводим имя , любое

---