#php #enum #snippet
Трейт для получение значений всех Enums, для *BackendEnum* отдаст всеих значения, а *PurpleEnum* значения выступаю название enums

---

🔹 *сам трейт*
```php
<?php

declare(strict_types=1);

use BackedEnum;

trait Values
{
    /** Get an array of case values. */
    public static function values(): array
    {
        $cases = static::cases();

        return isset($cases[0]) && $cases[0] instanceof BackedEnum
            ? array_column($cases, 'value')
            : array_column($cases, 'name');
    }
}
```

🔹 *Enums*
```php
enum StatusBackendEnum: int
{
    use Values;

    case Draft = 0;
    case New = 1;
    case Closed = 2;
}

enum StatusPurpleEnum
{
    use Values;

    case Draft;
    case New ;
    case Closed;
}
```

🔹 *Использование*
```php
$result = StatusBackendEnum::values()
var_dump($result);
// Выведет
[0, 1, 2]

$result = StatusPurpleEnum::values()
var_dump($result);
// Выведет
[
    "Draft"
    "New"
    "Closed"
]
```