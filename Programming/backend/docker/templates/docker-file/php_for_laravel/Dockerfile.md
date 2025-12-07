```Dockerfile
FROM php:8.1-fpm-alpine  
  
# Установка зависимостей  
RUN apk add --no-cache \  
    git \  
    zip \  
    unzip \  
    libpng-dev \  
    libzip-dev \  
    oniguruma-dev  
  
# Установка PHP расширений  
RUN docker-php-ext-install pdo pdo_mysql mbstring exif pcntl bcmath gd zip  
  
# Установка composer  
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer  
  
# Установка рабочей директории  
WORKDIR /var/www/html  
  
# Копирование файлов зависимостей  
COPY composer.json composer.lock ./  
  
# Установка зависимостей  
RUN composer install --no-dev --no-scripts --no-autoloader  
  
# Копирование остальных файлов проекта  
COPY . .  
  
# Генерация автозагрузчика  
RUN composer dump-autoload --optimize  
  
# Установка прав  
RUN chown -R www-data:www-data /var/www/html/storage  
RUN chmod -R 775 /var/www/html/storage  
  
# Настройка переменных окружения для Laravel  
ENV APP_ENV=production  
ENV APP_DEBUG=false  
  
# Определение тома для постоянных данных  
VOLUME /var/www/html/storage  
  
# Запуск PHP-FPM  
CMD ["php-fpm"]  
  
# Проверка работоспособности  
HEALTHCHECK --interval=30s --timeout=5s --start-period=30s --retries=3 \  
    CMD php artisan migrate:status || exit 1
```