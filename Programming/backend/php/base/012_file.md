#php #upload 

#### 🔹 Basic use

```php
// создает файл
touch($path); 

// показывает содержиое директории
glob('/var/tmp/*');
scandir('/var/tmp');

// Возвращает информацию о файле, $filename - путь к файлу
stat(string $filename);

// Удаление файла, $filename - путь к файлу
unlink(string $filename)

// Проверяет существует ли файл (или папка), $filename - путь к файлу
file_exists(string $filename): bool

// Перемещает загруженный файл в новое место
move_uploaded_file (string $filename, string $destination): bool
// $filename - путь к загруженому файлу
// $destination - путь ,куда будет перемещен файл

// Переименовать файл или директорию
rename(string $oldname, string $newname): bool

// копирует файл
сopy($old, $new)
```
---
#### 🔹 Чтение содержимого файла
- https://coder-booster.ru/learning/php-practice/line-and-whole-file-reading

```php
// Проверяет, достигнут ли конец файла
feof()

// показывает путь к файлу с которого был запущен скрипт
getcwd()

// построение путей
$pathParts = ['var', 'www', 'project'];
$path = implode(DIRECTORY_SEPARATOR, $pathParts)

// Файловый указатель
ftell($handle) // возвращает позицию указателя
fseek($handle, 0) // перемещает указатель
```
---
#### 🔹 Обьекто-орентированый подход

```php
$file = new \SplFileInfo(__FILE__);
$file->getPathInfo();
$file->getFileName();
$file->getExtension();
$file->isDir();
$file->isFile();
$file->isLink();
```
---
#### 🔹 Проверка файла

```php
if(!file_exists($path)){
	mkdir($patrh, 0755, $recursive)
}

is_dir($path);
is_file($path);
```
--- 
#### 🔹 Итераторы для работы с файлами

```php
//DirectoryIteratop, FilesystemIterator, RecursiveDirectoryIterator

$iterator = new \GlobIterator('../*');
foreach($iterator as $item){
	print_r($item)
}
```
---
#### 🔹 Чтение файла

```php
$file = __FILE__;

if(file_exists($file) && is_readable($file)){
	
	// первый вариант
	$handle = fopen($filename, "rb"); //r+
	if($handle){
		try {
			$contents = fread(handle, filesize($filename));
		} finally {	
			fclose($handle);
		}
	}

	//второй вариант
	$handle = fopen($filename, "rb"); //r+
	if($handle){
		try {
			while(!feof($handle)){
				echo fgets($handle, 1024);
			}
		} finally {	
			fclose($handle);
		}
	}

	//третий вариант
	$handle = fopen($filename, "rb"); //r+
	if($handle){
		try {
			while($info = fscanf($handle, "%s\t%s\t%s\n")){
				list($name, $profession, $countrycode) = $info
			}
		} finally {	
			fclose($handle);
		}
	}
}
```

##### 🔸 Обьектный подход
```php
$file = new SplFileObject("file.txt");

while(!$file->eof()){
	echo $file->fgets();
}

foreach($file as $lineNumber => $content){
	printf("Line %d: %s", $lineNumber, $content);
}

$lines = new LimitIterator(
	$file,
	9,		// start at line 10
	10		// iterate 10 lines
);
foreach($lines as $line){
	echo $line; // outputs line 10 to 20
}
```
---
#### 🔹 Писать в файл

```php
// первый вариант
file_put_contents($file, $data);

// второй вариант
if(is_writable($file)){
	$handle = fopen($file, "ab");	//a+ c
	if($handle){
		try {
			fwrite($handle, $data);
		} finally {
			fclose($handle);
		}
	}
}

// трерий вариант
$file = new \SplFileObject($file, "ab");
$file->fwrite($data);

```
---
