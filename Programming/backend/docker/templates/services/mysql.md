#docker #template #db #mysql 

### 🔹 Сервис для docker-compose

```yml
version: "3.7"
services:

  db:
    image: mysql:8
    container_name: mysql_db
    hostname: mysql_db
    restart: always
    environment:
      MYSQL_USER: root
      MYSQL_PASSWORD: root
      MYSQL_ROOT_USER: root
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: db
    ports:
      - 192.168.142.1:3306:3306
    volumes:
      - ./storage/mysql/:/var/lib/mysql/
      - ./storage/logs/mysql:/var/log/mysql

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin
    hostname: phpmyadmin
    links:
      - db
    environment:
      PMA_HOST: mysql_db
      PMA_PORT: 3306
    ports:
      - 192.168.142.1:8080:80
    volumes:
      - /sessions

networks:
  default:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.142.0/24

```

---