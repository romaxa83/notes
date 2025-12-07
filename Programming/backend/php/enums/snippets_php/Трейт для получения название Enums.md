#php #enum #snippet 
Трейт для получение названия всех Enums, **одинаково работает** и для *BackendEnum* и *PurpleEnum*

---

🔹 *сам трейт*
```php
<?php

declare(strict_types=1);

namespace App\Enums\Traits;

trait Names
{
    public static function names(): array
    {
        return array_column(static::cases(), 'name');
    }
}
```

🔹 *Enums*
```php
enum StatusBackendEnum: string
{
    use Names;

    case Draft = 'draft';
    case New = 'new';
    case Closed = 'closed';
}

enum StatusPurpleEnum
{
    use Names;

    case Draft;
    case New ;
    case Closed;
}
```

🔹 *Использование*
```php
$result = StatusBackendEnum::names()
var_dump($result);
// Выведет
[
    "Draft"
    "New"
    "Closed"
]

$result = StatusPurpleEnum::names()
var_dump($result);
// Выведет
[
    "Draft"
    "New"
    "Closed"
]
```