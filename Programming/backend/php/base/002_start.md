#php 

#### 🔹 Install

```bash
# Ubuntu
sudo add-apt-repository ppa:ondrej/php
sudo apt update
sudo apt install php8.0 php8.0-common php8.0-cli php8.0-fpm php8.0-gd php8.0-mysql php8.0-mbstring php8.0-curl php8.0-xml php8.0-zip php8.0-json
```

если нужно удалить версию
```bash
sudo apt purge php7.3*
```
---
#### 🔹 Terminal

```bash
показывает версию php(и можно выбрать какую использовать)
sudo update-alternatives --config php

показывает модули подключенные к php
php -m 
```
-------------------------------------
#### 🔹 Полезные вещи

##### 🔸 Увеличить кол-во передаваемых данных
```bash
# нужно в php.ini изменить поля
max_input_vars
post_max_size
```
-------------------------------------
#### 🔹 Articles

- ==Изменить версию php на маке== - https://ru.flamix.software/about/news-article/kak-smenit-versiyu-php-na-mac-os/
- ==Не игнорьте composer.lock== - https://medium.com/phpyh/dont-ignore-composer-lock-6f9fb3dc511a
---
