#php #enum #snippet #reflection #attribute
Данный подход позволяет добавить метаданные к занчениям Enum и использовать их в своих целях.Используются аттрибуты для добавления метаданных и рефлексия для их получения.

---
Разберем на примере добавления метаданных description и color, но добавить можно кастомный данные

🔹 *Создаем три класса для работы в папке Meta*

*Meta - используется добавления метаинформации к других классам*
```php
<?php

declare(strict_types=1);

namespace App\Enums\Meta;

use Attribute;

#[Attribute(Attribute::TARGET_CLASS)]
class Meta
{
    /** @var string[]|class-string<MetaProperty>[] */
    public array $metaProperties;

    public function __construct(array|string ...$metaProperties)
    {
        // When an array is passed, it'll be wrapped in an outer array due to the ...variadic parameter
        if (isset($metaProperties[0]) && is_array($metaProperties[0])) {
            // Extract the inner array
            $metaProperties = $metaProperties[0];
        }

        $this->metaProperties = $metaProperties;
    }
}
```

*MetaProperty - абстрактный класс для конкретных значений метаданных (в нашем кейсе это - descriptuion и color)*
```php
<?php

declare(strict_types=1);

namespace App\Enums\Meta;

abstract class MetaProperty
{
    final public function __construct(
        public mixed $value,
    ) {
        $this->value = $this->transform($value);
    }

    public static function defaultValue(): mixed
    {
        return null;
    }

    public static function make(mixed $value): static
    {
        return new static($value);
    }

    protected function transform(mixed $value): mixed
    {
        // Feel free to override this to transform the value during instantiation

        return $value;
    }

    /** Get the name of the accessor method */
    public static function method(): string
    {
        if (property_exists(static::class, 'method')) {
            return static::${'method'};
        }

        $parts = explode('\\', static::class);

        return lcfirst(end($parts));
    }
}
```

*Reflection - класс с помощью рефлексии получает метаданные из атрибутов*
```php
<?php

declare(strict_types=1);

namespace App\Enums\Meta;

use ReflectionAttribute;
use ReflectionClass;
use ReflectionEnumUnitCase;
use ReflectionObject;

class Reflection
{
    /**
     * @return string[]|array<\class-string<MetaProperty>>
     */
    public static function metaProperties(mixed $enum): array
    {
        $reflection = new ReflectionObject($enum);
        $metaProperties = static::parseMetaProperties($reflection);

        // Traits except the `Metadata` trait
        $traits = array_values(array_filter($reflection->getTraits(), fn (ReflectionClass $class) => $class->getName() !== 'ArchTech\Enums\Metadata'));

        foreach ($traits as $trait) {
            $metaProperties = array_merge(
                $metaProperties,
                static::parseMetaProperties($trait)
	        );
        }

        return $metaProperties;
    }

    /** @param ReflectionClass<object> $reflection */
    protected static function parseMetaProperties(
    ReflectionClass $reflection
    ): array
    {
        // Only the `Meta` attribute
        $attributes = $reflection->getAttributes(Meta::class);

        if ($attributes) {
            /** @var Meta $meta */
            $meta = $attributes[0]->newInstance();

            return $meta->metaProperties;
        }

        return [];
    }

    public static function metaValue(string $metaProperty, mixed $enum): mixed
    {
        // Find the case used by $enum
        $reflection = new ReflectionEnumUnitCase($enum::class, $enum->name);
        $attributes = $reflection->getAttributes();

        // Instantiate each ReflectionAttribute
        /** @var MetaProperty[] $properties */
        $properties = array_map(fn (ReflectionAttribute $attr) => $attr->newInstance(), $attributes);

        // Find the property that matches the $metaProperty class
        $properties = array_filter($properties, fn (MetaProperty $property) => $property::class === $metaProperty);

        // Reset array index
        $properties = array_values($properties);

        if ($properties) {
            return $properties[0]->value;
        }

        return $metaProperty::defaultValue() ?? null;
    }
}
```

🔹 *Создаем папку MetaProperties, шде будут храниться наши метаданныу*

*Description*
```php
<?php

declare(strict_types=1);

namespace App\Enums\MetaProperties;

use App\Enums\Meta\MetaProperty;
use Attribute;

#[Attribute]
class Description extends MetaProperty {}
```

*Color*
```php
<?php

declare(strict_types=1);

namespace App\Enums\MetaProperties;

use App\Enums\Meta\MetaProperty;
use Attribute;

#[Attribute]
class Color extends MetaProperty {}
```

🔹 *Трейт*
```php
<?php

declare(strict_types=1);

namespace App\Enums\Traits;

use App\Enums\Meta;
use ValueError;

trait Metadata
{
    /** Try to get the first case with this meta property value. */
    public static function tryFromMeta(
        Meta\MetaProperty $metaProperty
    ): static|null
    {
        foreach (static::cases() as $case) {
            if (Meta\Reflection::metaValue($metaProperty::class, $case) === $metaProperty->value) {
                return $case;
            }
        }

        return null;
    }

    /** Get the first case with this meta property value. */
    public static function fromMeta(Meta\MetaProperty $metaProperty): static
    {
        return static::tryFromMeta($metaProperty) ?? throw new ValueError(
            'Enum ' . static::class . ' does not have a case with a meta property "' .
            $metaProperty::class . '" of value "' . $metaProperty->value . '"'
        );
    }

    public function __call(string $property, $arguments): mixed
    {
        $metaProperties = Meta\Reflection::metaProperties($this);

        foreach ($metaProperties as $metaProperty) {
            if ($metaProperty::method() === $property) {
                return Meta\Reflection::metaValue($metaProperty, $this);
            }
        }

        return null;
    }
}
```

🔹 *Enums*
```php
<?php

namespace App\Enums\Test;

use App\Enums\Meta\Meta;
use App\Enums\MetaProperties\Color;
use App\Enums\MetaProperties\Description;
use App\Enums\Traits\Metadata;

/**
 * @method string description()
 * @method string color()
 */
#[Meta(Description::class, Color::class)]
enum StatusBackendEnum: string
{
    use Metadata;

    #[Description('Status as draft')] #[Color('red')]
    case Draft = 'draft';
    #[Description('Status as new')] #[Color('green')]
    case New = 'new';
    #[Description('Status as closed')] #[Color('black')]
    case Closed = 'closed';
}
```

🔹 *Варианты использования*

*получение значений метеданных*
```php
StatusBackendEnum::Draft->description() // Status as draft 
StatusBackendEnum::Draft->color() // red
```

*можно получать нужный enum по значению метаданных*
```php
StatusBackendEnum::fromMeta(Color::make('green')); // StatusBackendEnum::New
StatusBackendEnum::fromMeta(Color::make('blue')); // Error: ValueError

StatusBackendEnum::tryFromMeta(Color::make('green')); // StatusBackendEnum::New
StatusBackendEnum::tryFromMeta(Color::make('blue')); // null
```

🔹 *Кастомизация properties*

*изменяем вывод color*
```php
<?php

declare(strict_types=1);

namespace App\Enums\MetaProperties;

use App\Core\Enums\Meta\MetaProperty;
use Attribute;

#[Attribute]
class Color extends MetaProperty {

    protected function transform(mixed $value): mixed
    {
        return "text-{$value}-500";
    }
}
```
*теперь результат будет иной*
```php
StatusBackendEnum::Draft->color() // "text-red-500"
```

*изменяем метод вызова для description*
```php
<?php

declare(strict_types=1);

namespace App\Core\Enums\MetaProperties;

use App\Core\Enums\Meta\MetaProperty;
use Attribute;

#[Attribute]
class Description extends MetaProperty {

    public static function method(): string
    {
        return 'note';
    }
}
```

*теперь вызывать description нужно через note*
```php
StatusBackendEnum::Draft->description() // null
StatusBackendEnum::Draft->note() // "Status as draft"
```