#php #config

> Файл php.ini - основной конфигурационный файл PHP, который управляет поведением интерпретатора.

Типичные расположения:
- **Linux**: `/etc/php/8.4/apache2/php.ini`, `/etc/php/8.4/cli/php.ini`
- **Windows**: `C:\php\php.ini`
- **macOS**: `/usr/local/etc/php/8.4/php.ini`

==Основные принципы настройки php.ini:==
- **Безопасность превыше всего** - отключайте неиспользуемые функции
- **Оптимизируйте под нагрузку** - настраивайте лимиты памяти и времени
- **Используйте OPcache** - критично для производительности
- **Логируйте ошибки** - но не показывайте их пользователям на продакшене
- **Тестируйте изменения** - каждое изменение может повлиять на работу приложения
#### 🔹 Поиск активного файла php.ini

```bash
# Определение используемого php.ini
php --ini

# Или через PHP код
php -r "echo php_ini_loaded_file();"

# Просмотр всех настроек
php -i | grep "Loaded Configuration File"
```
---
#### 🔹 Основные разделы настроек
##### 🔸 Языковые настройки (Language Options)
```ini
; Включение коротких тегов <?= ?>
short_open_tag = On

; Точность вычислений с плавающей точкой
precision = 14

; Форматирование чисел по умолчанию
serialize_precision = -1

; Отключение небезопасных функций
disable_functions = exec,passthru,shell_exec,system,proc_open,popen

; Отключение классов
disable_classes = 

; Ограничение времени выполнения скрипта (в секундах)
max_execution_time = 30

; Ограничение времени разбора входных данных
max_input_time = 60

; Лимит памяти для скрипта
memory_limit = 128M
```

##### 🔸 Обработка ошибок и логирование
```ini
; Включение отображения ошибок
display_errors = Off

; Включение отображения ошибок при запуске
display_startup_errors = Off

; Уровень отчетности об ошибках
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT

; Логирование ошибок
log_errors = On

; Файл для записи ошибок
error_log = /var/log/php_errors.log

; Максимальная длина сообщения об ошибке
log_errors_max_len = 1024

; Игнорирование повторяющихся ошибок
ignore_repeated_errors = Off
```

##### 🔸 Безопасность (Data Handling)
```ini
; Переменные по умолчанию (deprecated с PHP 5.4)
register_globals = Off

; Автоматическое добавление слешей к кавычкам (deprecated с PHP 5.4)
magic_quotes_gpc = Off

; Максимальный размер POST данных
post_max_size = 8M

; Автоматическое определение типа файла
auto_detect_line_endings = Off

; Кодировка по умолчанию
default_charset = "UTF-8"
```

##### 🔸 Загрузка файлов (File Uploads)
```ini
; Разрешение загрузки файлов
file_uploads = On

; Директория для временных файлов
upload_tmp_dir = /tmp

; Максимальный размер загружаемого файла
upload_max_filesize = 2M

; Максимальное количество одновременно загружаемых файлов
max_file_uploads = 20
```

##### 🔸 Работа с ресурсами
```ini
; Лимит времени выполнения скрипта
max_execution_time = 30

; Лимит использования памяти
memory_limit = 128M

; Максимальная глубина вложенности массивов в POST/GET
max_input_nesting_level = 64

; Максимальное количество переменных в POST/GET/COOKIE
max_input_vars = 1000
```
---
#### 🔹 Критически важные настройки для продакшена

##### 🔸 Безопасность
```ini
; ПРОДАКШЕН - скрыть ошибки от пользователей
display_errors = Off
display_startup_errors = Off
log_errors = On
error_log = /var/log/php/error.log

; Скрыть версию PHP в заголовках
expose_php = Off

; Ограничить выполнение файлов только из разрешенных директорий
open_basedir = "/var/www/html:/tmp"

; Отключить опасные функции
disable_functions = exec,passthru,shell_exec,system,proc_open,popen,curl_exec,curl_multi_exec,parse_ini_file,show_source

; Включить строгую проверку типов
strict_types = 1
```

##### 🔸 Производительность
```ini
; Увеличить лимиты для высоконагруженных приложений
memory_limit = 512M
max_execution_time = 300
max_input_time = 300
post_max_size = 100M
upload_max_filesize = 50M

; Настройки для работы с большими данными
max_input_vars = 5000
max_input_nesting_level = 128
```
---
#### 🔹 OPcache настройки (критически важно для продакшена)
```ini
[opcache]
; Включение OPcache
opcache.enable=1

; Размер памяти для OPcache (в мегабайтах)
opcache.memory_consumption=128

; Количество файлов для кеширования
opcache.max_accelerated_files=10000

; Проверка изменений файлов
opcache.validate_timestamps=1

; Частота проверки изменений (в секундах)
opcache.revalidate_freq=2

; Сохранение комментариев
opcache.save_comments=1

; Быстрое выключение
opcache.fast_shutdown=1

; Включение CLI
opcache.enable_cli=0
```
---
#### 🔹 Настройки сессий
```ini
[Session]
; Автоматический старт сессии
session.auto_start = 0

; Время жизни сессии (в секундах)
session.gc_maxlifetime = 1440

; Вероятность запуска сборщика мусора сессий
session.gc_probability = 1
session.gc_divisor = 1000

; Папка для хранения сессий
session.save_path = "/tmp"

; Имя сессии
session.name = PHPSESSID

; Использование cookies только
session.use_only_cookies = 1

; Включение httponly для cookies сессии
session.cookie_httponly = 1

; Использование secure cookies (только HTTPS)
session.cookie_secure = 1

; Политика SameSite для cookies
session.cookie_samesite = "Strict"
```
---
#### 🔹 Настройки базы данных
##### 🔸MySQL/MariaDB
```ini
[mysql]
mysql.allow_local_infile = On
mysql.allow_persistent = On
mysql.cache_size = 2000
mysql.max_persistent = -1
mysql.max_links = -1
mysql.default_port = 3306
mysql.default_socket = /tmp/mysql.sock
mysql.default_host = localhost
mysql.default_user = 
mysql.default_password = 
mysql.connect_timeout = 60
mysql.trace_mode = Off
```

##### 🔸 PostgreSQL
```ini
[pgsql]
pgsql.allow_persistent = On
pgsql.auto_reset_persistent = Off
pgsql.max_persistent = -1
pgsql.max_links = -1
pgsql.ignore_notice = 0
pgsql.log_notice = 0
```
---
#### 🔹 Настройки почты
```ini
[mail function]
; Для Linux/Unix
sendmail_path = /usr/sbin/sendmail -t -i

; Для Windows
SMTP = localhost
smtp_port = 25

; Принудительное добавление заголовка X-PHP-Originating-Script
mail.add_x_header = Off

; Логирование отправки писем
mail.log = /var/log/php_mail.log
```
---
#### 🔹 Расширения и модули
```ini
; Динамическая загрузка расширений
extension=mysqli
extension=pdo_mysql
extension=curl
extension=gd
extension=mbstring
extension=xml
extension=zip
extension=json
extension=openssl
extension=redis
extension=memcached

; Настройки для конкретных расширений
[curl]
curl.cainfo = "/path/to/cacert.pem"

[gd]
gd.jpeg_ignore_warning = 1

[mbstring]
mbstring.language = Russian
mbstring.internal_encoding = UTF-8
mbstring.http_input = auto
mbstring.http_output = UTF-8
mbstring.encoding_translation = Off
mbstring.detect_order = auto
mbstring.substitute_character = none
```
---
#### 🔹 Практические конфигурации
##### 🔸 Конфигурация для разработки
```ini
; === DEVELOPMENT SETTINGS ===
display_errors = On
display_startup_errors = On
error_reporting = E_ALL
log_errors = On
error_log = /var/log/php/dev_errors.log

memory_limit = 256M
max_execution_time = 0
post_max_size = 50M
upload_max_filesize = 20M

; OPcache для разработки
opcache.enable = 1
opcache.validate_timestamps = 1
opcache.revalidate_freq = 0
opcache.max_accelerated_files = 3000
```

##### 🔸 Конфигурация для продакшена
```ini
; === PRODUCTION SETTINGS ===
display_errors = Off
display_startup_errors = Off
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT
log_errors = On
error_log = /var/log/php/production_errors.log
expose_php = Off

memory_limit = 128M
max_execution_time = 30
post_max_size = 8M
upload_max_filesize = 2M

; Безопасность
disable_functions = exec,passthru,shell_exec,system
open_basedir = "/var/www/html:/tmp"

; OPcache для продакшена
opcache.enable = 1
opcache.validate_timestamps = 0
opcache.max_accelerated_files = 20000
opcache.memory_consumption = 256
opcache.fast_shutdown = 1
```
##### 🔸 Конфигурация для высоконагруженных систем
```ini
; === HIGH LOAD SETTINGS ===
memory_limit = 512M
max_execution_time = 300
post_max_size = 100M
upload_max_filesize = 50M
max_input_vars = 10000

; OPcache оптимизация
opcache.enable = 1
opcache.memory_consumption = 512
opcache.max_accelerated_files = 100000
opcache.validate_timestamps = 0
opcache.save_comments = 0
opcache.fast_shutdown = 1

; Настройки для работы с большими объемами данных
realpath_cache_size = 4096K
realpath_cache_ttl = 600
```
---
#### 🔹 Проверка и валидация настроек
```ini
# Проверка синтаксиса php.ini
php --ini | head -1 | awk '{print $NF}' | xargs php -c

# Получение конкретной настройки
php -r "echo ini_get('memory_limit');"

# Просмотр всех настроек
php -i

# Создание информационной страницы
```

```php
<?php  
// info.php - НЕ оставляйте на продакшене!  
phpinfo();  
?>
```
---