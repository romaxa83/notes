#docker #template #db  #mongo

### 🔹 Сервис для docker-compose

```yml
version: "3.7"
services:

  mongo:
    image: mongo:3.6
    container_name: mongo
    volumes:
      - ./docker/mongo/db:/data/db
      - ./docker/mongo/log/:/var/log/mongodb/
    ports:
      - "27017:27017"

networks:
  default:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.145.0/24
```

---

