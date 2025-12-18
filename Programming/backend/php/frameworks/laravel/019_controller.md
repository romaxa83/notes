#php #laravel 

#### 🔹 Контролер одного действия

```php
// создания
php artisan make:controller ShowProfile --invokable

// нужно использовать метод __invoke()

class ShowProfile extends Controller
{
    public function __invoke($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}

// Для такого контролера используеться такой маршрут
Route::get('user/{id}', 'ShowProfile');

```
---