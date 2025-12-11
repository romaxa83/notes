#php #datetime

> Есть хороша библиотека по работе с датой здесь о ней подробнее [[004_carbone|Carbone]]
#### 🔹 Настройка временной зоны в `php`

- http://linux-notes.org/nastrojka-timezone-v-php-ini/
---
#### 🔹 Перевод даты в unix-формат

```php
$date = '2016-05-24 16:32:45';
$timestamp = strtotime($date);
```
---
#### 🔹 Обьект `\DateTimeImmutable`

> Данный класс ведет себя аналогично классу `DateTime`, за исключением того, что он никогда не изменяет себя, а вместо этого возвращает новый объект.

```php
// возвращает текущую дату
$date = new \DateTimeImmutable('now');
// форматирует дату
$date->format('Y-m-d');
// добавит к дате 7 дней
$date->modify('7 days')

$date = new DateTime("1899-12-31");
$date->getTimestamp();
```
---
#### 🔹 DateHelper

```php
class DateHelper
{
    private $timeZone = 'Europe/Kiev';

    private $format = 'd-m-Y H:i:s';

    public function now($format = false)
    {
        return ($this->nowTime())->format($format ?: $this->format);
    }

    public function nowTimestamp()
    {
        return ($this->nowTime())->getTimestamp();
    }

    public function formatTimestamp($timestamp,$format = false)
    {
        return date($format ?: $this->format,$timestamp);
    }

    private function nowTime()
    {
        return new \DateTimeImmutable('now',new \DateTimeZone($this->timeZone));
    }
}
```
---
