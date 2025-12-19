```bash
# создаем образ описаный в Dockerfile с именем "go_dt:v1"
docker build -t go_dt:v1 .

# посмтортеть что он создался
docker images

# запустить докер-образ
docker run go_dt:v1

#### опубликовать образ в DockerHub
# логинимся DockerHub
docker login
# указываем имя, под которым локальный образ будет храниться в Docker Hub
docker tag go_dt:v1 "romaxa/go_dt:v1"
# пушим
docker push "romaxa/go_dt:v1"

# удалить образ
docker rmi -f <IMAGE ID>
```

