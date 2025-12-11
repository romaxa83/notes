#php #laravel 

#### 🔹 Использования UUID

==создаем нужное поле в бд==
```php
Schema::create('posts', function (Blueprint $table) {
    $table->uuid('id')->primary();
});
```

==создаем трейт app/Traits/UsesUuid.php==
```php
use Illuminate\Support\Str;
trait UsesUuid
{
	// see-1
    protected static function bootUsesUuid()
    {
        static::creating(function ($model) {
            if (! $model->getKey()) {
                $model->{$model->getKeyName()} = (string) Str::uuid();
            }
        });
    }

    // see-2
    public function getIncrementing()
    {
        return false;
    }

    // see-3
    public function getKeyType()
    {
        return 'string';
    }
}
```

==see-1==
в методе `boot` мы подключаемся к нашей модели и прослушиваем событие создание модели

==see-2==
так как мы используем `UUID`, поэтому устанавливаем `false` на автоинкремент

==see-3==
в метод `getKeyType` указываем, что идентификаторы в таблице должны храниться в виде строк

---