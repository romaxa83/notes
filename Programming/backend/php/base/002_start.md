#php 

какие встроеные класса есть в php

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
- ==PHP Правильный путь.== - http://getjump.github.io/ru-php-the-right-way/
- ==RoadRunner (связка php+go)== - https://habr.com/ru/company/badoo/blog/434272/
- ==Доступ к защищеным полям== - https://medium.com/@frontman/php-access-to-private-and-protected-b1028b974169
- ==Архитектура высоких нагрузок== - https://ruhighload.com/%D0%90%D1%80%D1%85%D0%B8%D1%82%D0%B5%D0%BA%D1%82%D1%83%D1%80%D0%B0+%D0%B2%D1%8B%D1%81%D0%BE%D0%BA%D0%B8%D1%85+%D0%BD%D0%B0%D0%B3%D1%80%D1%83%D0%B7%D0%BE%D0%BA
- ==Асинхроный php== - https://habr.com/ru/post/427589/
- ==APiDocs== - https://elisdn.ru/blog/80/some-reasons-to-learn-phpdoc
- ==Вывод в реальном времени результатов выполнения shell_exec в PHP==  - https://zalinux.ru/?p=452
-  ==Мануал по созданию и форматированию excel файлов в PHPExcel== - https://snipp.ru/php/phpexcel
- ==Laravel eloquent advanced== - https://habr.com/ru/articles/762598/
- ==Шпаргалка по современому PHP== - https://github.com/smknstd/modern-php-cheatsheet

---
