#php #curl 

**Curl** - это программное обеспечение, которое позволяет выполнять запросы разных типов или протоколов

### 🔹 PHP cURL основы

```php
// инициализирует сессию работы с cURL
curl_init([string $url = NULL]);

// изменяет поведение cURL-сессии, в соответствии с переданными опциями
curl_setopt(resource $ch , int $option , mixed $value);

// выполняет cURL запрос по сконфигурированной сессии, и возвращает результат
curl_exec(resource $ch);

// закрывает сессию cURL и удаляет переменную, которой присвоен curl_init();
curl_close(resource $ch); 
```

##### 🔸 Отправка GET запроса из PHP cURL
```php
$queryParams = [
    'page' => 1,
    'user' => 'truehero',
];

// URL страницы, которую открываем
$url = 'https://some-site.ua?' . http_build_query($queryParams)

$ch = curl_init($url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

$response = curl_exec($ch);
curl_close($ch);
```

##### 🔸 Отправка POST запроса из PHP cURL
```php
// данные POST-запроса
$data = [
    'event' => 'Page',
    'url' => 'http://some-site.ua'
];

// url, на который отправляет данные
$url = 'https://some-site.ua/log_analitycs';

$ch = curl_init($url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POST, true);
// POST-параметры, указанные в массиве, в веде ключ => значение
curl_setopt($ch, CURLOPT_POSTFIELDS, $data); 

$response = curl_exec($ch);

curl_close($ch);

var_dump($response);

// если данные от API получаем в JSON, то, декодируем их
$result = json_decode($response, true); // ['success': true, ...]
```

##### 🔸 Отправка заголовков (Headers)
```php
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'Custom-Header-1: The-Header-Value-1',
    'Custom-Header-1: The-Header-Value-2'
]);
```

##### 🔸  Отправка JSON запроса
по дефолту данные отправляются через `Form-Data`, чтоб отправить в виде `JSON`, нужно перекодировать данные в JSON вручную, а также передать **соответствующие HTTP-заголовки**
```php
$data = [
    'site' => 'https://badcode.ru',
    'action' => 'subscribe',
    'email' => 'bafisto@bigmir.net',
];
$dataString = json_encode($data);

$url = 'http://localhost/handler.php';

$ch = curl_init($url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $dataString);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'Content-Type: application/json',
    'Content-Length: ' . strlen($dataString)
]);

$result = curl_exec($ch);
curl_close($ch);
```