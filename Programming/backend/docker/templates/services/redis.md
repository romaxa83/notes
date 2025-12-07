#docker #template #db #redis

### 🔹 Сервис для docker-compose

```yml
version: "3.7"
services:

  redis:
    image: redis:7-alpine
    container_name: redis
    hostname: redis
    ports:
      - 192.168.143.5:6379:6379
    volumes:
      - ./storage/redis:/data
    command:
      - 'redis-server'
      - '--databases 2'
      - '--save 900 1' # сохраняем бекап на диск каждые 900 сек если было одно изменение
      - '--save 300 10'
      - '--save 60 10000'
      - '--requirepass secret'
	healthcheck:  
	  test: redis-cli ping  
	  interval: 5s  
	  timeout: 10s  
	  retries: 20

networks:
  default:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.143.0/24
```

---

