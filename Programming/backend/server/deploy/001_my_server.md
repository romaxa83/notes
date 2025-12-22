
#### 🔹 DOMAIN

- регистрировал на - https://www.ukraine.com.ua
- админ панель - https://adm.tools/domains/
- проверка ip сайта по домену - https://sitechecker.pro/ru/domain-to-ip/

```
email - romaxa83@ukr.net
password - RoMaXa1983_domain
```
---
#### 🔹 VSCALE (хостинг)
 - https://vscale.io/panel/settings/user/
 - сервер - https://vscale.io/panel/scalets/1654518/
```
Логин: 64428
Пароль: FFVTp0W8qr

ssh root@80.249.144.7
```
---
#### 🔹 AWS

- https://us-east-2.console.aws.amazon.com/console/home?region=us-east-2#
```
ROOT_USER
email - romaxa83@ukr.net
password - RoMaXa1983_aws
name - romaxa
```
---
#### 🔹 Deploy micro

поднять две виртуалки (debian10) 
	1 - реестр для docker образов с доменом `registry.cubic-dev.tech`
	2 - для сайт 
привязать ip к domen


==Развертывание реестра==
- переходим в проект реестра в папку provisioning
- копируем host.yml.dist в host.yml и прописываем ip реестра
- make server - установит на сервере docker,certbot, настроит сертификаты
- make authorize - прокидываем публичный клю пользователю deploy
- переходим в корень проекта
- docker run --rm registry:2 htpasswd -Bbn user password > htpasswd
    генерим пароль для аутентификации к регистру (вместо user и password соответствено вводим данные)
- деплоим -
    HOST=deploy@ip_host PORT=22 HTPASSWD_FILE=htpasswd make deploy
    где
        HOST - ssh подключение к серверу
        PORT - порт подключения
        HTPASSWD_FILE - путь к файлу с генерированым паролем

- https://registry.cubic-dev.tech/v2/_catalog - получаем все каталоги в реестре        

==Развертывания сайта==
- переходим в проект в папку provisioning
- копируем host.yml.dist в host.yml и прописываем ip сайта
- make site
- make auth
- make docker-login
- переходим в корень проекта
- docker login -u user -p password registry.cubic-dev.tech
- REGISTRY=registry.cubic-dev.tech IMAGE_TAG=root-3 make build-prod (собираем образы для продакта)
- REGISTRY=registry.cubic-dev.tech IMAGE_TAG=root-3 make push (пушим образы в регистр)
- HOST=deploy@84.38.181.12 PORT=22 REGISTRY=registry.cubic-dev.tech IMAGE_TAG=root-3 BUILD_NUMBER=3 make deploy (разворачиваем сайт)

- https://cubic-dev.tech/ - сайт
- https://api.cubic-dev.tech/ - api
---