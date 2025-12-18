#php #laravel #encrypt

#### 🔹 Hashid

- https://julienbourdeau.com/laravel-hashid

> Использование Hashid вместо UUID в LaravelHashid — пакет для генерации уникальных коротких хэшей.

```php
$h = new Hashids\Hashids('this is my salt');
$hashid = $h->encode(125); // mD1
$id = $h->decode($hashid); // [125]
```
---
