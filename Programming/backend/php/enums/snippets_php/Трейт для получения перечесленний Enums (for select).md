#php #enum #snippet
Трейт для получение Enums в виде ассоциативного массива который удобен для селектов,а также можно вызывать строку в виде options. **Одинаково работает** и для *BackendEnum* и *PurpleEnum*

---

🔹 *сам трейт*
```php
<?php

declare(strict_types=1);

use BackedEnum;
use Closure;

trait Options
{
    /** Get an associative array of [case name => case value] or an indexed array [case name, case name] in the case of pure enums. */
    public static function options(): array
    {
        $cases = static::cases();

        return isset($cases[0]) && $cases[0] instanceof BackedEnum
            ? array_column($cases, 'value', 'name')
            : array_column($cases, 'name');
    }

    /**
     * Generate a string format of the enum options using the provided callback and glue.
     * @param Closure(string $name, mixed $value): string $callback
     */
    public static function stringOptions(?Closure $callback = null, string $glue = '\n'): string
    {
        $firstCase = static::cases()[0] ?? null;

        if ($firstCase === null) {
            return '';
        } elseif ($firstCase instanceof BackedEnum) {
            // [name => value]
            $options = static::options();
        } else {
            // [name, name]
            $options = static::options();

            // [name => name, name => name]
            $options = array_combine($options, $options);
        }

        // Default callback
        $callback ??= function ($name, $value) {
            if (str_contains($name, '_')) {
                // Snake case
                $words = explode('_', $name);
            } elseif (strtoupper($name) === $name) {
                // If the entire name is uppercase without underscores, it's a single word
                $words = [$name];
            } else {
                // Pascal case or camel case
                $words = array_filter(preg_split('/(?=[A-Z])/', $name));
            }

            return "<option value=\"{$value}\">" . ucfirst(strtolower(implode(' ', $words))) . '</option>';
        };

        $options = array_map($callback, array_keys($options), array_values($options));

        return implode($glue, $options);
    }
}
```

🔹 *Enums*
```php
enum StatusBackendEnum: string
{
    use Options;

    case Draft = 'draft';
    case New = 'new';
    case Closed = 'closed';
}

enum StatusPurpleEnum
{
    use Options;

    case Draft;
    case New ;
    case Closed;
}
```

🔹 *Использование*
```php
$result = StatusBackendEnum::options()
var_dump($result);
// Выведет
[
    "Draft" => "draft"
    "New" => "new"
    "Closed" => "closed"
]

$result = StatusBackendEnum::stringOptions()
var_dump($result);
// Выведет
"<option value="draft">Draft</option>\n<option value="new">New</option>\n<option value="closed">Closed</option>"

$result = StatusPurpleEnum::options()
var_dump($result);
// Выведет
[
		"Draft"
    "New"
    "Closed"
]

$result = StatusBackendEnum::stringOptions()
var_dump($result);
// Выведет
"<option value="Draft">Draft</option>\n<option value="New">New</option>\n<option value="Closed">Closed</option>"
```