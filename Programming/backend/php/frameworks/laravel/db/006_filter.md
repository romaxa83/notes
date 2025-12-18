#php #laravel #database #sql

- https://habr.com/ru/post/485520/

#### 🔹 Реализация QueryFilter

> Для каждой сущности создается отдельный класс, сопоставляющий методы с каждым полем, по которым предстоит фильтрация.

> Ниже пример реализации класс для фильтрации модели `Category` для полей `id`, `name`, `userId`, для каждого поля создается свой метод для фильтрации.

```php
namespace App\ModelFilters;

use EloquentFilter\ModelFilter;
use Illuminate\Database\Eloquent\Builder;

class CategoryFilter extends ModelFilter
{
    public function __construct($query, array $input = [], $relationsEnabled = true)
    {
        $this->drop_id = false; // cut _id false
        parent::__construct($query, $input, $relationsEnabled);
    }

    public function id(int $id)
    {
        return $this->where(
            function (Builder $query) use ($id) {
                return $query->where('id', '=', $id);
            }
        );
    }

    public function name(string $name)
    {
        return $this->where(
            function (Builder $query) use ($name) {
                return $query
                    ->where('name', 'like', '%' . $name . '%');
            }
        );
    }

    public function userId(int $userId)
    {
        return $this->where(
            function (Builder $query) use ($userId) {
                return $query->where('user_id', '=', $userId);
            }
        );
    }
}

```

> в базовую модель (Category), добавляем трейт `use Filterable;`

```php
# при запросе используем scope
$categories = Category::filter($filter)->limit(10)->get();

# где filter = [
#	'id' => 2,
#	'name' => 'some name',
#	'user_id' => 2,
#]
```
---
#### 🔹 Пример для фильтрации связи

```php
public function directionId($directionId)
{
    return $this->where(
        function (Builder $query) use ($directionId) {
            return $query
                ->whereHas(
                    'direction',
                    function (Builder $builder) use ($directionId) {
                        return $builder->where('directions.id', '=', $directionId);
                }
            );
        }
    );
}

```
---
#### 🔹 Пример фильтрации если приходит или масиив id или одно id

```php
public function creatorId($creatorId)
{
    return $this->where(
        function (Builder $query) use ($creatorId) {
            if(is_array($creatorId)){
                return $query->whereIn('creator_id', $creatorId);
            } else {
                return $query->where('creator_id', $creatorId);
            }
        }
    );
}


// тот же флоу только со связью
public function directionId($directionId)
{
    return $this->where(
        function (Builder $query) use ($directionId) {
            return $query
                ->whereHas(
                    'direction',
                    function (Builder $builder) use ($directionId) {
                        return $builder->when(
                            is_array($directionId),
                            function (Builder $q) use ($directionId) {
                                return $q->whereIn('directions.id', $directionId);
                            }
                        )->when(
                            !is_array($directionId),
                            function (Builder $q) use ($directionId) {
                                return $q->where('directions.id', '=', $directionId);
                            }
                        );
                    }
            );
        }
    );
}
```
---