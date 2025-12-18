#php #laravel #log #monitor 

#### 🔹 log-viewer

> пакет для просмотра логов приложения в браузере

```bash
composer require opcodesio/log-viewer
```
---
#### 🔹 Custom logger

> создание отдельного файла для логов

```php
use Monolog\Logger;
use Monolog\Handler\StreamHandler;

$logger = new Logger('Laravel-Test');
$logger->pushHandler(new StreamHandler(storage_path('logs/laravel-test.log')));

$logMessage = 'notification send to ';

$logger->info($logMessage, [
    'test' => 'some',
]);
```
---
#### 🔹 Custom Logger (v2)

==в файле config/logging.php, настраиваем свой логгер==
```php
"channels" => [
    "email" => [
        "driver" => "single",
        "path" => storage_path("logs\emails.log")
        "level" => 'info'
    ],
    .........
]
```

==вызываем в коде так==
```php
use Illuminate\Suppor\Facades\Log;

Log::channel('email')->info("message");
```
---