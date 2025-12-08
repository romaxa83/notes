#php #laravel #database #migration

- http://unetway.com/tutorial/laravel-migracii/

#### 🔹 Команда для создания

```bash
php artisan make:migration create_user_table

# table=users - название таблицы 
php artisan make:migration add_user_table --table=users

# перестроит миграции и запустит сиды
php artisan migrate:refresh --seed
```
---
#### 🔹 Структура

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateUsersTable extends Migration
{
    public function up(): void
    {
        Schema::create('users', function (Blueprint $table) {
            $table->bigIncrements('id');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('users');
    }
}
```

==В ввиде коллбека==
```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('technicians',
            static function (Blueprint $table) {
                $table->id();
                // another fields
            }
        );
    }

    public function down(): void
    {
        Schema::dropIfExists('technicians');
    }
};
```
---
#### 🔹 Переименовать таблицу

```php
Schema::rename('old_table', 'new_table');
```
---
#### 🔹 Обновление данных

```php
# если в таблице уже есть пользователи, то им нужно проставить в это поле значенния,после Schema пишем код и он , тем пользователям, которые уже есть в таблице, проставит активный статус

DB::table('users')->update([
	'status' => 'active',
]);
```
---
#### 🔹 Fields

```php
// для часовых поясов
$table->timestampsTz();

// создаст поля created_at и updated_at
$table->timestamps();

// поле timestamp (useCurrent - по дефолту, текущее время)
$table->timestamp('updated_at')->useCurrent();

// создать связь
$table->unsignedBigInteger('user_id');
$table->foreign('user_id')
    ->references('id')
    ->on('users')
    ->onDelete('cascade');

// откат при добавление связи
$table->dropForeign('users_user_id_foreign');
$table->dropColumn('user_id');


// уникальный ключ
$table->unique(['role_id', 'lang']);

// использование uuid как id записи, подробнее в разделе 005_model/24
$table->uuid('id')->primary();

// поле enum
$table->enum('user_type', ['admin', 'user'])->default('user');

$table->geometry('positions');
$table->ipAddress('visitor');
$table->macAddress('device');
$table->point('position');

// поля для soft-deleted
$table->softDeletes();
$table->dropSoftDeletes();

// создание первично ключа (составного)
$table->primary(['user_id', 'book_id'], 'pk-name_primary');
```

##### 🔸 Добавить внешний ключ и удалить его
```php
public function up(): void
{
    Schema::table('admins', function (Blueprint $table) {
        $table->unsignedBigInteger('department_id')
            ->after('dealership_id')->nullable();
        $table->foreign('department_id')
            ->references('id')
            ->on('dealership_departments')
            ->index('idx_admins_department_id')
            ->onDelete('cascade');
    });
}

public function down(): void
{
    Schema::table('admins', function (Blueprint $table) {
        $table->dropForeign('idx_admins_department_id');
        $table->dropColumn(['department_id']);
    });
}
```

##### 🔸 Проверка наличия таблицы / столбца
```php
if (Schema::hasTable('users')) {
    // Таблица `users` существует ...
}

if (Schema::hasColumn('users', 'email')) {
    // Таблица `users` существует и содержит столбец `email` ...
}
```
---
#### 🔹 Если при добавление нового поля, нужно , всем записям добавить значение на основе другого поля 

```php
DB::table(Invoice::TABLE_NAME)->update([
    'drivers_amount' => DB::raw('amount')
]);
```
---
#### 🔹 ЕСЛИ нужно миграцию применить на другой таблице

```php
public function up(): void
{
    Schema::connection(DbConnections::GPS)
        ->table(History::TABLE_NAME, function (Blueprint $table) {
            $table->integer('sleep_mode')->nullable();
    });
}
```
---