#recipes #php #curl 

Есть скрипт на PHP, который должен отправлять множество HTTP-запросов. Нужно сделать это как можно быстрее.Какие есть решения.

#### 🔹 Последовательная обработка

Простой цикл с `curl_init()` и `curl_exec()` на каждый URL. Результат: 10 запросов выполняются за **~4.4 секунды**.

```php
<?php

$urls = [
    'https://example.com/',
    'https://example.com/1',
    'https://example.com/2',
    'https://example.com/3',
    'https://example.com/4',
    'https://example.com/5',
    'https://example.com/6',
    'https://example.com/7',
    'https://example.com/8',
    'https://example.com/9',
];

$total_start = microtime(true);

foreach ($urls as $url) {
    $request_start = microtime(true);
    $handle = curl_init();
    curl_setopt($handle, CURLOPT_URL, $url);
    curl_setopt($handle, CURLOPT_RETURNTRANSFER, true);
    $response = curl_exec($handle);
    $response_code = curl_getinfo($handle, CURLINFO_HTTP_CODE);
    $request_duration = number_format(microtime(true) - $request_start, 3);

    echo "Response code for request to {$url} is {$response_code} in {$request_duration}s\n";
}

$total_duration = number_format(microtime(true) - $total_start, 3);
echo "All requests took {$total_duration}s\n";
```
---
#### 🔹 Повторное использование `curl`-хэндла

Инициализируем `curl` один раз и переиспользуем. Время выполнения снижается до **~1.7 секунды**.

```php
<?php

$urls = [
    'https://example.com/',
    'https://example.com/1',
    'https://example.com/2',
    'https://example.com/3',
    'https://example.com/4',
    'https://example.com/5',
    'https://example.com/6',
    'https://example.com/7',
    'https://example.com/8',
    'https://example.com/9',
];

$total_start = microtime(true);

$handle = curl_init();
curl_setopt($handle, CURLOPT_RETURNTRANSFER, true);

foreach ($urls as $url) {
    $request_start = microtime(true);
    curl_setopt($handle, CURLOPT_URL, $url);
    $response = curl_exec($handle);
    $response_code = curl_getinfo($handle, CURLINFO_HTTP_CODE);
    $request_duration = number_format(microtime(true) - $request_start, 3);

    echo "Response code for request to {$url} is {$response_code} in {$request_duration}s\n";
}

$total_duration = number_format(microtime(true) - $total_start, 3);
echo "All requests took {$total_duration}s\n";

```
---
#### 🔹 Параллельная отправка с `curl_multi`

Используем curl_multi_init() и запускаем запросы одновременно. Итог: **всего 0.5 секунды** на 10 запросов

```php
<?php

$urls = [
    'https://example.com/',
    'https://example.com/1',
    'https://example.com/2',
    'https://example.com/3',
    'https://example.com/4',
    'https://example.com/5',
    'https://example.com/6',
    'https://example.com/7',
    'https://example.com/8',
    'https://example.com/9',
];

$total_start = microtime(true);

$multi_handle = curl_multi_init();
$handles = [];
for ($i = 0; $i < count($urls); ++$i) {
    $handles[$i] = curl_init();
    curl_setopt($handles[$i], CURLOPT_RETURNTRANSFER, true);
    curl_setopt($handles[$i], CURLOPT_URL, $urls[$i]);
    curl_multi_add_handle($multi_handle, $handles[$i]);
}

$running = null;
do {
    curl_multi_exec($multi_handle, $running);
    curl_multi_select($multi_handle);
} while ($running > 0);

for ($i = 0; $i < count($urls); $i++) {
    $response_code = curl_getinfo($handles[$i], CURLINFO_HTTP_CODE);

    echo "Response code for request to {$urls[$i]} is {$response_code}\n";
}

$total_duration = number_format(microtime(true) - $total_start, 3);
echo "All requests took {$total_duration}s\n";
```
---
#### 🔹 Отправка батчами (`batching`)

Если запросов сотни или тысячи, одновременно всё не потянет даже мощный сервер. Решение — отправка пакетами, например по 3. Это позволяет контролировать нагрузку. В нашем примере — **~0.8 секунды** на 10 запросов

```php
<?php

$urls = [
    'https://example.com/',
    'https://example.com/1',
    'https://example.com/2',
    'https://example.com/3',
    'https://example.com/4',
    'https://example.com/5',
    'https://example.com/6',
    'https://example.com/7',
    'https://example.com/8',
    'https://example.com/9',
];

$total_start = microtime(true);

$multi_handle = curl_multi_init();
$batch_size = 3;

$handles = [];
for ($i = 0; $i < $batch_size; ++$i) {
    $handles[$i] = curl_init();
    curl_setopt($handles[$i], CURLOPT_RETURNTRANSFER, true);
}

$batch = [];
foreach ($urls as $index => $url) {
    $batch[] = $url;

    if (count($batch) % $batch_size !== 0 && $index < count($urls) - 1) {
    continue;
    }

    for ($i = 0; $i < count($batch); ++$i) {
    curl_multi_add_handle($multi_handle, $handles[$i]);
    curl_setopt($handles[$i], CURLOPT_URL, $batch[$i]);
    }

    $running = null;
    do {
    curl_multi_exec($multi_handle, $running);
    curl_multi_select($multi_handle);
    } while ($running > 0);

    for ($i = 0; $i < count($batch); ++$i) {
    $response_code = curl_getinfo($handles[$i], CURLINFO_HTTP_CODE);

    echo "Response code for request to {$batch[$i]} is {$response_code}\n";

    curl_multi_remove_handle($multi_handle, $handles[$i]);
    }

    $batch = [];
}

$total_duration = number_format(microtime(true) - $total_start, 3);
echo "All requests took {$total_duration}s\n";
```
---