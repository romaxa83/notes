#php #enum #snippet 
Трейт для статического получение названия Enums, т.е. если вызвать enum так `StatusBackendEnum::Draft`, то мы получим обьект enum, а если нам нужно тольео значение, то его нужно дополнительно вызывать через метод value - `StatusBackendEnum::Draft->value,` с помощбю трейта можно сразу получить значение так `StatusBackendEnum::Draft()`.

---

🔹 *сам трейт*
```php
<?php

declare(strict_types=1);

use BackedEnum;

trait InvokableCases
{
    public function __invoke()
    {
        return $this instanceof BackedEnum ? $this->value : $this->name;
    }

    /** Return the enum's value or name when it's called ::STATICALLY(). */
    public static function __callStatic($name, $args)
    {
        $cases = static::cases();

        foreach ($cases as $case) {
            if ($case->name === $name) {
                return $case instanceof BackedEnum ? $case->value : $case->name;
            }
        }

        throw new UndefinedCaseError(static::class, $name);
    }
}
```

🔹 *Enums*
```php
// для нормальной работы автокомплита в IDE
/**
 * @method static Draft()
 * @method static New()
 * @method static Closed()
 */

enum StatusBackendEnum: string
{
    use InvokableCases;

    case Draft = 'draft';
    case New = 'new';
    case Closed = 'closed';
}

enum StatusPurpleEnum
{
    use InvokableCases;

    case Draft;
    case New ;
    case Closed;
}
```

🔹 *Использование*
```php
$result = StatusBackendEnum::Draft()
echo ($result);
// Выведет
"draft"

$result = StatusPurpleEnum::Draft()
echo ($result);
// Выведет
"Draft"
```