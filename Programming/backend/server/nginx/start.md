#server #nginx 

#### 🔹 Установка из исходников (Ubuntu)
```bash
sudo apt-get update
sudo apt-get install build-essential

# на оф. сайте (https://www.nginx.com/resources/wiki/start/topics/tutorials/install/) копируем ссылку с версию mainline и скачиваем в терминале

wget https://nginx.org/download/nginx-1.9.3.tar.gz
tar -zxvf nginx-1.9.3.tar.gz
cd nginx-1.9.3

# устанавливаем зависимости
sudo apt-get install libpcre3 libpcre3-dev libpcrecpp0v5 libssl-dev zlib1g-dev

# в папки nginx-1.9.3 запускаем configure , его можно запускать с настройками. перечень всех настроек (https://www.nginx.com/resources/wiki/start/topics/tutorials/installoptions/)
sbin-path=/usr/bin/nginx # куда установим исполняем файл также можно устанавливать модули

 ./configure --sbin-path=/usr/bin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --with-debug --with-pcre --with-http_ssl_module

make
```

- `/etc/nginx/conf.d/`  - содержит устанавливаемый по умолчанию файл конфигурации сервера HTTP
- `/var/log/nginx/access.log` - логи всех запросов к серверу
- `/var/log/nginx/error.log` - логи ошибок
---
#### 🔹  Команда Nginx

```bash
nginx -h # меню подсказок
nginx -v # версия сервера (если -V - более подробная инфа)
nginx -t # проверка конфигурации 
nginx -T # проверяет конфигурацию NGINX и выводит на ваш экран допустимые настройки
nginx -s signal # Данный флаг -s отправляет некий сигнал имеющемуся процессу хозяина NGINX. Вы имеете возможность отправлять такие сигналы как stop, quit, reload и reopen. Сигнал stop немедленно отключает имеющийся процесс NGINX. Синал quit останавливает процесс NGINX после того как он завершит обработку влетевших в него запросов. Сигнал reload перезагружает данную конфигурацию. Сигнал reopen инструктирует NGINX повторно открыть файлы журналов.

# выведет все модули nginx
nginx -V 2>&1 | tr -- - '\n' | grep module   
```
---
#### 🔹 Полезные ссылки
- курс на YouTube по nginx - https://www.youtube.com/watch?v=dHsv8EnhSgQ&list=PLhgRAQ8BwWFa7ulOkX0qi5UfVizGD_-Rc
- Документация - http://nginx.org/ru/docs/
- Основы - http://nginx.org/ru/docs/beginners_guide.html
---
