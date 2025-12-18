#php #laravel #database 

#### 🔹 Команда для запуска

```bash
# запуск сидов
php artisan db:seed

# запуск конкретного сида
php artisan db:seed --class=UsersTableSeeder
```
---
#### 🔹 FACTORY

```php
// создаем пользователя
$user = factory(User::class)->create();

// создает модель, но не сохраняет в бд
$user = factory(User::class)->make();

// если нужно несколько пользователей
$user = factory(User::class)->times(5)->create();

// можно использовать функцию обратного вызова для выполнения каких-либо действий после вставки записи
$factory->afterCreating(App\User::class, function ($user, $faker) {
    $user->accounts()->save(factory(App\Account::class)->make());
});
```
---
#### 🔹 Удаление данных

> если нужно удалить данные из таблицы и при этом обнулить инкремент данный вариант не удаляет связанные данные

```php
\DB::statement('SET FOREIGN_KEY_CHECKS=0;');
\DB::table('dealerships')->truncate();
\DB::table('dealership_translations')->truncate();
\DB::statement('SET FOREIGN_KEY_CHECKS=1;');
```
---
