#http-server #tools

> Утилита позволяет сделать локальный сервер доступным всему интернету по специальному `HTTPS` адресу

- DOCS - https://ngrok.com/docs

```bash
# install ubuntu
sudo snap install ngrok

# запустит сервер на 80 порту
ngrok http 80
#выведет приблизительно такой url https://af25a9680c90.ngrok.io доступный в инете

# если сайт не localhost, а к примеру на 192.168.177.1 то запускаем так
ngrok http -host-header=rewrite 192.168.177.1:80
```
---


