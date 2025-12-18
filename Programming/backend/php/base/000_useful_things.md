#php 

#### 🔹 Получить имя домена из ссылки (URL)

```php
function stripToDomainName($uri = '')
{
	$uri = strtolower(trim($uri));
	$uri = preg_replace('%^(http:\/\/)*(www.)*%usi', '', $uri);
	$uri = preg_replace('%\/.*$%usi' , '', $uri);
	
	return $uri;
}

/* Example */
$url = 'http://www.hostname.tld/path/?argument=value#anchor';

echo stripToDomainName($url); // hostname.tld
```
---
#### 🔹 Цикл с шагом

===================================
getenv() - получение переменых окружения
putenv() - установка переменых окружения

===================================
call_user_func - Вызывает callback-функцию, заданную в первом параметре

===================================
$_SERVER['REMOTE_ADDR'] — массив, в котором содержится IP адрес пользователя, открывшего ваш сайт.
$_SERVER['HTTP_USER_AGENT'] — а это данные его браузера, через который он зашёл на ваш сайт.

===================================
Цикл с шагом

<?php for($i = 0;$i < $count;$i=($i+2)):?>
        <li class="page-item active">
            <a href="#" class="page-link">1</a>
        </li>
<?php endfor;?>

===================================
Сделать многомерный массив в один
$iterator = new RecursiveIteratorIterator(new RecursiveArrayIterator($arrIn));
$arrOut = iterator_to_array($iterator, false);

===================================
получение всех данных от клиента (в виде строки)
file_get_contents('php://input');

===================================
присваевает в переменые значения массива
list()

===================================
Проверка времени скрипта
$start = microtime(true);

    $hotels_id = HotelReview::find()->select('hotel_id')->where(['status' => HotelReview::ACTIVE])->asArray()->all();

$time = microtime(true) - $start;

===================================
РЕКУРСИВНО ПАРСИТ ВСЕ ФАЙЛЫ В ПАПКЕ
    $root = '/etc';

    $iter = new RecursiveIteratorIterator(
        new RecursiveDirectoryIterator($root, RecursiveDirectoryIterator::SKIP_DOTS),
        RecursiveIteratorIterator::SELF_FIRST,
// при блоке прав чтения не отвалится
        RecursiveIteratorIterator::CATCH_GET_CHILD // Ignore "Permission denied" (>>на которую у него нет прав на чтение)
    );

    $paths = array($root);
    foreach ($iter as $path => $dir) {
        if ($dir->isDir()) {
            $paths[] = $path;
        }
    }

    print_r($paths);

===================================
gc_collect_cycles() - принудительный запуска сборщика мусора

===================================
Методы для cors

header('Access-Control-Allow-Origin: *');
header('Access-Control-Allow-Credentials: true');
header('Access-Control-Allow-Methods: GET,POST,PUT,DELETE,HEAD,OPTIONS');
header('Access-Control-Allow-Headers: Origin,Content-Type,Accept,Authorization');

===================================
вывод времени работы скрипта

private function microtime_float()
{
    list($usec, $sev) = explode(' ', microtime());
    return ((float)$usec + (float)$sec);
}

public function someAction()
{
    $time_start = $this->microtime_float();

    // some logic

    $time_end = $this->microtime_float();
    $time = $time_end - $time_start;

    return $time . " sec";
}


===================================
Проверка на true или false (если оно ввиде строки)

filter_var('false', FILTER_VALIDATE_BOOLEAN);

===================================
===================================
===================================
===================================