#deploy #devops #server

#### 🔹 Deploy приложение на nodejs (вручную)

- mongodb+srv://romaxa:LATKYkewda2T3oOi@cluster0-vnd12.mongodb.net/camp
- Manual - https://gist.github.com/bradtraversy/cd90d1ed3c462fe3bddd11bf8953a896

##### 🔸 устанавливаем `nodejs`

```bash
sudo apt update
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt install nodejs
```

##### 🔸 стягиваем проект

```bash
# создаем папку app
mkdir app && cd app

# клонируем проект
git clone https://github.com/romaxa83/camp.git

# устанавливаем конфиги
cp config.env.dist config.env

# устанавливаем зависимости
npm i
```

##### 🔸 устанавливаем `pm2`

- https://pm2.keymetrics.io/

```bash
npm install pm2 -g

# запускаем
pm2 start server.js
pm2 status # смотрим запущеные процесы
pm2 restart server # перезапускаем
pm2 stop idProcess # останавливает процесс для определеного id
pm2 logs # выводит логи
pm2 startup ubuntu
```

##### 🔸 Ставим `nginx` ,как прокси сервер, и настраиваем его

```bash
sudo apt install nginx -y
# перейдя на домен, должна быть заглушка nginx

sudo nano /etc/nginx/sites-available/default
# чистим секцию server и вставляем
```

```ini
server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:8000; #указываем порт на котором работает наше приложение
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```

```bash
sudo service nginx restart # перезапускаем nginx
sudo nginx -t # проверяем корректны нашам конфигурация
```

##### 🔸 ставим firewall

- https://1cloud.ru/help/security/ispolzovanie-utility-ufw-na-inux

```bash
sudo apt install ufw

ufw status
ufw enable # включаем
ufw allow ssh # устанавливаем ssh как единственную точку входа через порт22
ufw status
ufw allow http # даст вход в приложение по http
ufw allow https
```

##### 🔸 открываем доступ по `ssh`, `http`, `https`

```bash
### SSL

# установка для Ubuntu 
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx

# установка для Debian
sudo apt install python3-acme python3-certbot python3-mock python3-openssl python3-pkg-resources python3-pyparsing python3-zope.interfac
sudo apt-get install python-certbot-nginx
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```
--------------------------------------------------------

 


