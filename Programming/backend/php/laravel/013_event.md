#php #laravel 

#### 🔹 Глобальное событие

> Глобальные события устанавливаются в  папке `Providers -> AppServiceProviders.php` в методе `boot` прописываются действия на определенный события

```php
// перед создание региона запуститься анонимная функция, которая очистит кеш по тегу регион
Region::created(function(){
	Cache::tags('region')->flush();
});
```

==события==:
- updated - после обновления
- updating - перед обновлением
- saved
- deleted
- deleting
---
#### 🔹 Создание события

- создаем событие по пути `app\Events` приблизительно такой структуры
```php
namespace App\Events;

use Illuminate\Queue\SerializesModels;

class CreateOrder
{
    use SerializesModels;

    public function __construct(
        public Order $order,
    )
    {}
}
```

- создаем обработчик для события по пути `app\Listeners` 
```php
namespace App\Listeners;

class SendOrderListeners
{
	// метод обрабатывает событие
    public function handle(CreateOrder $event)
    {
        try {
            // some logic
        } catch (\Throwable $e) {
            \Log::error($e->getMessage());
        }
    }
}
```

- регистрируем обработчики в провайдере `EventServiceProvider` , одно событие могут слушать несколько обработчиков
```php
protected $listen = [
    CreateOrder::class => [
        SendOrderListeners::class,
    ],
];
```

- в коде запускаем событие
```php
event(new CreateOrder($order));
```
---