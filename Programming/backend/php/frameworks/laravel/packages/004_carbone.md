#php #laravel #package #datetime

- https://carbon.nesbot.com/docs/
#### 🔹 Basic Use

```php
// Перевод из одного формата (d/m/y) в другой (F d,Y)
Carbon::createFromFormat('d/m/y',$this->date)->format('F d,Y');

// копирует переданую дату и добавляет к ней 15 дней
$published = $date->copy()->addDays(15);

// отнимает дату
$date->copy()->subDays(15);

// Перевод даты в unix-метку
Carbon::parse($date)->timestamp;

// из timestamp в нужный формат
Carbon::createFromTimestamp($timestamp)->format('Y-m-d H:i:s');

// начало текущего дня
$now = Carbon::today();

// разница в часах с начала дня
$now = Carbon::today();
$diff = Carbon::now()->diffInHours($now);

// человеко понятный вывод разницы времени
$user->last_login_at->diffForHumans();
```

- eq() equals
- ne() not equals
- gt() greater than
- gte() greater than or equals
- lt() less than
- lte() less than or equals
---