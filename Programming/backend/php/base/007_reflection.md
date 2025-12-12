#php 

==Обертка над Reflection API==
https://github.com/Roave/BetterReflection
---
#### 🔹 Получение методов класса

```php
$reflection = new ReflectionClass('backend\modules\user\controllers\ReviewsController');
$method = $reflection->getMethods();
```
---
#### 🔹 Получение содержание файла (рекурсивно)

```php
$rii = new RecursiveIteratorIterator(new RecursiveDirectoryIterator($path));

$files = array();

foreach($rii as $file) {
    if ($file->isDir()){
        continue;
    }
   $files[] = $file->getPathname();
}
var_dump($files);
```

---