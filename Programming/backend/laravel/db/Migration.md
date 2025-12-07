#laravel #db #migration

#### Добавление поля к существующей таблицы

```php
return new class extends Migration {  
    public function up(): void  
    {  
        Schema::table(
            User::TABLE, 
            function (Blueprint $table) {  
                $table->string('name');  
	        }
		);  
    }  
  
    public function down(): void  
    {  
        Schema::table(
            User::TABLE,
            function (Blueprint $table) {  
                $table->dropColumn('name');  
            }
        );  
    }  
};
```

#### Добавление внешнего ключа
```php
table->unsignedInteger('user_id')->nullable();  
$table->foreign('user_id')  
    ->references('id')  
    ->on(User::TABLE)  
    ->onUpdate('cascade')  
    ->onDelete('cascade');
```

если добавление внешних ключей происходит для уже существующей таблица, то в метод `down()` добавляем удаления поля и индекса для внешнего ключа

```php
$table->dropForeign(['user_id']); 
$table->dropColumn(['user_id']);
```

> ⚠️ если добавляется несколько внешних ключей то при удалении индекса (`dropForeign`) нужно прописывать каждый отдельно, тогда тогда как удалять поля можно через массив в методе `dropColumn`
#### Поля

* Для **Float**
```php
$table->decimal('sum', 10, 2)
```