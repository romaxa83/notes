#php #enum #snippet #laravel
Трейт для получение правила валидации в Laravel на основе значений всех Enums. В Laravel есть правило валидации `in:` которое указывает какие допустимые значения для этого поля. Трейт позволяет сразу формировать данное правило. **Работате только для BackendEnum**.

---

🔹 *сам трейт*
```php
<?php

declare(strict_types=1);

trait RuleIn
{
    public static function ruleIn()
    {
        $items = array_map(fn($case) => $case->value, self::cases());

        return 'in:' . implode(',', $items);
    }
}
```

🔹 *Enums*
```php
enum StatusBackendEnum: string
{
    use RuleIn;

    case Draft = 'draft';
    case New = 'new';
    case Closed = 'closed';
}
```

🔹 *Использование*
```php
$result = StatusBackendEnum::ruleIn()
echo($result);
// Выведет
"in:draft,new,closed"
```