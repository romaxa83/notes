#php #laravel #terminal 

#### 🔹 Базовая структура

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;

class TestCommand extends Command
{
    protected $signature = 'cmd:test';

    protected $description = 'Test command';

    public function __construct()
    {
        parent::__construct();
    }

    public function handle()
    {}
}
```
---
#### 🔹 Ввод данных

```php
$this->ask('Enter Login');
```
---
#### 🔹 Потверждение

```php
$this->confirm('Do you want delete this model? [y|N]');
```
---
#### 🔹 Флаги

```php
// указываем в команде {--f}
protected $signature = 'cmd:test {--f}';
// если нужно передовать значение
protected $signature = 'cmd:test {--f=}';

// получаем так (true/false - если был передан, или значение)
$this->option('f');

// php artisan cmd:test --flag

class TestCommand extends Command
{
    // так указываем флаг со значением
    protected $signature = 'cmd:test {--f=20}';

    public function handle()
    {
        // так получаем
        $flag = $this->option('f');

        // при вызове php artisan cmd:test
        dd($flag); // "20"
    }
}

// вызываем команду с флагом
// php artisan cmd:test --f

// вызываем команду со значением
// php artisan cmd:test --f=test
```
---
#### 🔹 Аргументы

```php
// указываем в команде {arg}
protected $signature = 'cmd:test {arg}';
// если аргумент не обязателен
protected $signature = 'cmd:test {arg?}';

// получаем так
$arg = $this->argument('arg');

// вызываем команду с аргументом
php artisan cmd:test someArg

```
---
#### 🔹 Выбор данных

```php
$choiceYear = $this->choice(
    'Choice year',
    [2020, 2021],
);

// Вопрос с опцией автозаполнения
$name = $this->anticipate('Как тебя зовут?', ['Taylor', 'Dayle']);
```
---
#### 🔹 Вывод текстовой информации

```php
// Default color
$this->line('This is a line');
// Yellow collor
$this->warn('This is a warning');
$this->comment('This is a comment');
// White text on red background
$this->error('This is an error');
// Black text on cyan background
$this->question('This is a question');
// Green color
$this->info('This is some info');

$this->line('<bg=black> My awesome message </>');
$this->line('<fg=green> My awesome message </>');
$this->line('<bg=red;fg=yellow> My awesome message </>');
$this->line('<bg=red;fg=yellow> My awesome message </>');

$this->line("<options=bold;fg=red> MY AWESOME MESSAGE </>");
$this->line("<options=bold;fg=red> MY AWESOME MESSAGE </>");
$this->line("<options=underscore;bg=cyan;fg=blue> MY MESSAGE </>");
```
---
#### 🔹 Вызов команды из кода

```php
// с передоваемыми аргументами
Artisan::call('db:seed', [
    '--class' => $seederClassName,
]);
```
---
#### 🔹 Прогресс бар

- https://symfony.com/doc/current/components/console/helpers/progressbar.html

```php
// инициализация
use Symfony\Component\Console\Helper\ProgressBar;

$count = count($models);
$progressBar = new ProgressBar($this->output, $count);
$progressBar->setFormat('verbose');
$progressBar->start();

// подсчитываемое действие
$progressBar->advance();

$progressBar->finish();
echo PHP_EOL;

// если что-то пошло не так
$this->progressBar->clear();
```
---
#### 🔹 Вывод данных в таблице

- https://symfony.com/doc/current/components/console/helpers/table.html

```php
$headers = [
    [new TableCell('A list of all users', ['colspan' => 4])],
    ['id', 'name', 'email', 'email verified at']
];

$tableStyle = (new TableStyle())
    ->setCellHeaderFormat('<fg=black;bg=yellow>%s</>');
Table::setStyleDefinition('secrets', $tableStyle);


$users = User::query()->get();
$data = $users->map(function (User $user) {
    return [
        'id' => $user->id,
        'name' => $user->name,
        'email' => $user->email,
        'email_verified_at' => $user->hasVerifiedEmail()
            ? $user->email_verified_at->format('Y-m-d')
            : 'Not verified',
        ];
    });

$percentageVerified = 6;

$data->push(new TableSeparator());
$data->push([new TableCell(
    sprintf('%d%% verified by email', $percentageVerified),
        ['colspan' => 4]
    )]);

$this->table($headers, $data, 'secrets');
```
---
#### 🔹 Команда через замыкание

==создаеться в файле routes/console.php==
```php
use Illuminate\Support\Facades\Artisan;

// отправляем email и подсчитываем время выполнения команды
Artisan::command('send:emails', function(){
    $start = now();
    $this->comment('Processing');
    app(UserController::class)->sendEmails();
    $time = $start->diffInSeconds(now());
    $this->comment("Processed in $time seconds");
});
```
---
#### 🔹 Schedule

- запускает команда в фоне в указаное время запуск команд указываем в методе `schedule` класса `Console/Kernel.php`
- на сервере в cron вставляем команду

```bash
* * * * * php /home/wezom/web/costback.wezom.agency/public_html/artisan schedule:run >>/dev/null 2>&1
  
# где /home/wezom/web/costback.wezom.agency/public_html/ - полный путь до проекта
```
---
#### 🔹 Artisan

```bash
// выводит список все команд
php artisan

// запускает php сервер с проектом
php artisan serve

// сгенерирует контролер Home  в папке Admin
php artisan controller:make Admin\\HomeController;
// сгенерирует контролер Home  в папке Admin c загатовками для crud
php artisan controller:make Admin\\HomeController --resource

// создаст модель Project, где
// m - создать файл миграции
// c - создать контроллер
// r - контроллер должен быть REST
php artisan make:model Models/Project -mcr
    
//появляеться интерактивный режим для php(для php скриптов)
php artisan tinker;

// создает middleware с именем AdminMiddleware
php artisan make:middleware AdminMiddleware;

// очищает кеш
php artisan config:clear;

// точная версия лары
php artisan --version
```
---
#### 🔹 Tinker

```php
// быстро очистить данные в таблице
App\Models\User:truncate();

// выведет все записи
App\Models\User::all();

// можно подключить модель, и затем ее использовать
use App\Models\User;
User::find(11);
User::find([1,2,3]);
```
---