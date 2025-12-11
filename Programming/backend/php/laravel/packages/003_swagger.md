#php #laravel #api-docs

#### 🔹 Install

 - https://github.com/DarkaOnLine/L5-Swagger

```bash
# ставим пакеты
composer require darkaonline/l5-swagger
composer require zircote/swagger-php

# публикуем конфигурацию
php artisan vendor:publish --provider "L5Swagger\L5SwaggerServiceProvider"

# генерируем документацию (котороая доступна по /api/documentation)
php artisan l5-swagger:generate

# если возникла ошибка
Symfony\Component\Debug\Exception\FatalThrowableError : Call to undefined function OpenApi\scan()
# то добавляем строку SWAGGER_VERSION=2.0 в файл .env
```
---