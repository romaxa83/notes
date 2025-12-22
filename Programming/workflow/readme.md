
https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

AWESOME README
https://github.com/matiassingers/awesome-readme
================
examlpe
<div id="top"></div>
# Backend для проекта Армо моторс

<p align="center">
  <img src="docs/diagrams/app.png" alt="app">
</p>

[![PHP](https://img.shields.io/badge/php-%5E8.0-blue)](https://www.php.net/)
[![Framework](https://img.shields.io/badge/laravel-8-red)](https://laravel.com/docs/8.x)
[![Database](https://img.shields.io/badge/mysql-8-green)](https://dev.mysql.com/doc/refman/8.0/en/)
[![GraphQL](https://img.shields.io/badge/graphql-lighthouse-lightgrey)](https://lighthouse-php.com/)
[![Cache](https://img.shields.io/badge/cache-redis-yellow)](https://redis.io/)



<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about">О проекте</a>
    </li>
    <li>
        <a href="#deploy">Разворачивание проекта</a>
        <ul>
            <li><a href="#deploy-docker">Разворачивание проекта через docker</a></li>
            <li><a href="#env-file">Переменный окружения</a></li>
        </ul>
    </li>
    <li><a href="#translations">Переводы</a></li>
    <li><a href="#roles">Ролевая система</a></li>
    <li><a href="#dump">Dump</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->
## О проекте
<div id="about"></div>
Проект является бэкендом для мобильного приложения (в дальнейшем МП) 
и административной панелью (в дальнейшем АП) компании <a href="https://www.arma-motors.ua/">Арма-моторс</a>  
(продажа, сервис обслуживания автомобилей),
общение осуществляется с помощью
GraphQL. Также осуществляется синхронизация (двухсторонняя) с системой учета 1С (в дальнейшем AA), 
через REST-API (<a href="https://app.swaggerhub.com/apis-docs/AuditSoft/MDMAPI/1.0.0">документация</a> 
описана с помощью swagger). Важная информация по проекту, 
диаграммы процессов и связей находятся в папке docs

<!-- Deployment -->
## Разворачивание проекта
<div id="deploy"></div>

1. Клонируем env файл

```sh
$ cp .env.example .env
```

2. Прописываем переменный окружения
   
3. Раскомментируем seed, после загрузки данных, имеет смысл их
   заново закомментировать, кроме сидов с разрешениями (подробнее здесь)
4. Выполнить последовательно команды
```sh
$ composer install
$ php artisan key:generate
$ php-fpm php artisan migrate
$ php-fpm php artisan db:seed
$ php-fpm php artisan passport:keys
$ php-fpm php artisan am:init # создает суперпользователя
$ php artisan am:translates-flow
$ sudo chmod 777 -R storage
```

5. Перенести секреты и id, для авторизации из бд (oauth_clients)
    в .env файл

<p align="right">(<a href="#top">back to top</a>)</p>

### Разворачивание проекта через docker
<div id="deploy-docker"></div>

проект и зависимости (бд, кеш, майлер) можно поднять через docker
выполняем шаги 1-3 
запускам инициализацию проекта (выполняется один раз)


<p align="right">(<a href="#top">back to top</a>)</p>


<p align="right">(<a href="#top">back to top</a>)</p>

<!-- Translations -->
## Переводы
<div id="translations"></div>
Системный переводы заносятся в resource/lang/en
команда ниже перегоняет переводы из файлов в бд (для двух языков - ru, ua)
а затем из бд в файлы ru и ua, записываются в бд только те переводы которых нет.
Это реализовано для того что бы дать возможность редактировать из админ-панели

```
php artisan am:translates-flow
```

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- Roles -->
## Ролевая система
<div id="roles"></div>

роли есть только у сотрудников, в системе есть одна предустановленная роль (super-admin) и 
пользователь с этой ролью, данной роли не присваиваются разрешения 
(у нее есть все разрешения по умолчанию). Другие роли создаются в админке, им 
присваиваются разрешения, пользователям присваивается роль, разрешения не создаются, 
они предустановленны в системе.

Разрешения добавляются через файл database/seeders/_permissions.csv в таком формате

```
role.get,Получить роль,Отримати роль,Роли,Pолі,1
```

1 эл. - алиас разрешения
2,3 эл. - название роли (рус/укр)
4,5 эл. - название группы разрешений
6 эл. - сортировка группы


##### Схема связей ролей и разрешений
<img src="docs/diagrams/relations/roles.png" alt="Logo" width="80" height="80">



## Supported types
* UInt8, UInt16, UInt32, UInt64, UInt128, UInt256
* Int8, Int16, Int32, Int64, Int128, Int256
* Date, Date32, DateTime, DateTime64
* Decimal32, Decimal64, Decimal128, Decimal256
* IPv4, IPv6
* String, FixedString(N)
* UUID
* Array(T)
* Enum8, Enum16
* LowCardinality(T)
* Map(K, V)
* Bool
* Tuple(T1, T2, ..., Tn)
* Nullable(T)

## TODO
- [ ] Connection pools
- [ ] TLS
- [ ] API UX Improvements (with 1.18 generics?)
    - [x] Enum
    - [x] LowCardinality
    - [ ] Array(T)
    - [ ] FixedString(N)
    - [ ] Map(K, V)
    - [ ] [Decimal(P, S)](https://clickhouse.com/docs/en/sql-reference/data-types/decimal/)
    - [ ] Nullable(T)
    - [ ] Tuple?
- [ ] Code generation from DDL
  - [ ] Parser
  - [ ] Code generator for SELECT/INSERT
  - [ ] Query builder
- [ ] DSL for DDL
- [ ] `database/sql` integration
- [ ] Reading and writing *Native* format dumps