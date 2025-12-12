#laravel #php #database 

### 🔹 Добавление собственных классов и методов в Query Builder и Коллекции Laravel Моделей

В Laravel есть способ добавить к моделям **кастомные методы билдера SQL запросов и коллекций**, можете вынести все *scoup* методы в отдельный класс, убрав методы запросов к БД и коллекциям, оставляя лишь бизнес-логику.

==FLOW==
###### 🔸Есть класс для логирования активности пользователей
```php
class Activity extends Model
{
    protected $table = 'activity_logs';
    protected $fillable = [
        'type',
        'status',
        'properties',
        'user_id'
    ];

    protected $casts = [
        'properties' => 'json',
    ];
}
```

###### 🔸Создаём кастомный класс Query Builder-а
```php
use Illuminate\Database\Eloquent\Builder;

class ActivityQueryBuilder extends Builder
{
    // метод поиска по типу
    public function byType(string $type): self
    {
        $this->where('type', $type);

        return $this;
    }

    // метод поиска по статусу
    public function byStatus(string $status): self
    {
        $this->where('status', $status);

        return $this;
    }

    // метод поиска по идентификатору пользователя
    public function byUserId(string $id): self
    {
        $this->where('user_id', $id);

        return $this;
    }
}
```

###### 🔸Добавляем кастомный Билдер в Модель
```php
class Activity extends Model
{
    // ...
    
    public function newEloquentBuilder($query): ActivityQueryBuilder
    {
        return new ActivityQueryBuilder($query);
    }
}
```

###### 🔸 Создаём кастомный класс коллекции Модели
```php
use Illuminate\Database\Eloquent\Collection;

class ActivityCollection extends Collection
{
    // метод коллекции - фильтрация по статусу
    public function onlySuccess(): self
    {
        $this->where('status', 'success');

        return $this;
    }
}
```

>⚠️ Примечание: несмотря на то, что функция `where` в коллекции будет делать то же самое, что и в билдере, лучше делать как можно больше в запросах к базе данных (Query Builder). Функция "where" в запросе к базе данных будет быстрее, чем "where" в коллекции.

---