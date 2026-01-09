#php  #recipes 

#### 🔹 `isset()` vs `empty()` vs `is_null()`

 ==Когда что использовать?==
🔹 `isset($var)` → Проверяем, существует ли переменная.
🔹 `empty($var)` → Проверяем, «пустая» ли она (`falsey`).
🔹 `is_null($var)` → Проверяем, является ли `null`.

```php
isset($var) // Проверяет, существует ли переменная и не равна ли она null.
isset($var) // → false, если $var = null; или переменная не объявлена.
isset($var) // → true, если $var = «», 0, false, [], но НЕ null.

empty($var) // Проверяет, считается ли переменная «пустой» (falsey значением).
empty($var) // → true, если $var = «», 0, false, null, [], «0».
empty($var) // → false, если $var = «abc», 123, [1,2,3].

is_null($var) // Проверяет, является ли переменная строго null.
is_null($var) // → true, только если $var = null.
is_null($var) // → false, если $var существует и имеет любое другое значение (даже «», 0, false).
```

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
#### 🔹 Данные запроса

```php
$_SERVER['REMOTE_ADDR'] // массив, в котором содержится IP адрес пользователя, открывшего ваш сайт.

$_SERVER['HTTP_USER_AGENT'] // а это данные его браузера, через который он зашёл на ваш сайт.
```
---
#### 🔹 Цикл с шагом

```php
for($i = 0;$i < $count;$i=($i+2))
```
---
#### 🔹 Сделать многомерный массив в один

```php
$iterator = new RecursiveIteratorIterator(new RecursiveArrayIterator($arrIn));
$arrOut = iterator_to_array($iterator, false);
```
---
#### 🔹 получение всех данных от клиента (в виде строки)

```php
file_get_contents('php://input');
```
---
#### 🔹 РЕКУРСИВНО ПАРСИТ ВСЕ ФАЙЛЫ В ПАПКЕ

```php
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
```

---
#### 🔹 Принудительный запуска сборщика мусора

```php
gc_collect_cycles() 
```
---
#### 🔹 Вывод времени работы скрипта

```php
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
```
---
#### 🔹 Проверка на `true` или `false` (если оно ввиде строки)

```php
filter_var('false', FILTER_VALIDATE_BOOLEAN);
```
---


