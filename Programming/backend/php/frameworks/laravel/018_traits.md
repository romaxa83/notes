#php #laravel 

#### 🔹 Tappable

> есть удобный метод `tap`, который позволяет вызывать замыкание с заданным значением и затем возвращать значение

```php
// without tap:
function getServer($id) {
    $server = ServerModel::findOrFail($id);
    Log::debug('Server was found', $server->toArray());
    return $server;
}

// with tap:
function getServer($id) {
    return tap(ServerModel::findOrFail($id), function ($server) {
        Log::debug('Server was found', $server->toArray());
    });
}
```

> трейт `Tappable` является оберткой глобального хелпере `tap()` для модели, которую можно использовать в коллекции

```php
ServerModel::active()
    ->paid()
    ->withTrashed()
    ->findOrFail($id)
    ->tap(function ($server) {
        Log::debug('Server was found', $server->toArray());
    });
```
---
