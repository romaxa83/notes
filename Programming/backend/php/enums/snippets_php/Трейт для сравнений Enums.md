#php #enum #snippet 
Трейт для сравненния значений Enums, **одинаково работает** и для *BackendEnum* и *PurpleEnum*

---

==🔹 *Трейт*==
```php
<?php

declare(strict_types=1);

namespace App\Enums\Traits;

use Exception;
use Iterator;
use IteratorAggregate;

trait Comparable
{
    public function is(mixed $enum): bool
    {
        return $this === $enum;
    }

    public function isNot(mixed $enum): bool
    {
        return !$this->is($enum);
    }

    public function in(array|object $enums): bool
    {
        $iterator = $enums;

        if (! is_array($enums)) {
            if ($enums instanceof Iterator) {
                $iterator = $enums;
            } elseif ($enums instanceof IteratorAggregate) {
                $iterator = $enums->getIterator();
            } else {
                throw new Exception('in() expects an iterable value');
            }
        }

        foreach ($iterator as $item) {
            if ($item === $this) {
                return true;
            }
        }

        return false;
    }

    public function notIn(array|object $enums): bool
    {
        return !$this->in($enums);
    }
}
```

===🔹 *Enums*===
```php
<?php

use App\Enums\Traits\Comparable;


enum StatusBackendEnum: string
{
    use Comparable;

    case Draft = 'draft';
    case New = 'new';
    case Closed = 'closed';
}
```

==🔹 *Использование*==
```php
// is
StatusBackendEnum::Draft->is(StatusBackendEnum::Draft) // true
StatusBackendEnum::Draft->is(StatusBackendEnum::New) // false

// isNot
StatusBackendEnum::Draft->isNot(StatusBackendEnum::Draft) // false
StatusBackendEnum::Draft->isNot(StatusBackendEnum::New) // true

// in
StatusBackendEnum::Draft->in([StatusBackendEnum::Draft, StatusBackendEnum::New]) // true
StatusBackendEnum::Draft->in([StatusBackendEnum::Closed, StatusBackendEnum::New]) // false
	
// notIn
StatusBackendEnum::Draft->notIn([StatusBackendEnum::Draft, StatusBackendEnum::New]) // false
StatusBackendEnum::Draft->notIn([StatusBackendEnum::Closed, StatusBackendEnum::New]) // true	
```