#docker #template #db #rabbitmq

### 🔹 Сервис для docker-compose

```yml
version: "3.7"
services:

  rabbitmq:
    image: rabbitmq:3-management
    container_name: rabbitmq
    hostname: rabbitmq
    environment:
      RABBITMQ_ERLANG_COOKIE: SWQOKODSQALRPCLNMEQG
      RABBITMQ_DEFAULT_USER: rabbit
      RABBITMQ_DEFAULT_PASS: rabbit
    ports:
      - 192.168.143.1:15672:15672 # порт по которому открываеться админка rabbit
      - 192.168.143.1:5672:5672 # порт по которому работает rabbit
    volumes:
      # прокидаваем конфиг для плагинов
      - ./enabled_plugins:/etc/rabbitmq/enabled_plugins

networks:
  default:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.143.0/24
```


для плагинов создаем файл - **enabled_plugins** , где прописываем подключаемые плагины, к примеру:

[rabbitmq_management, rabbitmq_management_visualiser].

---

### 🔹 Запуск одной командой

```bash
docker run -d --hostname my-rabbit --name some-rabbit -p 15672:15672 -p 5672:5672 rabbitmq:3-management
```

----

