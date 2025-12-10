#php #laravel #middleware

#### 🔹 Create

```php
// создать middleware
php artisan make:middleware AdminMiddleware

// затем в файле app->Http->Kernel.php в секции routeMiddleware прописавыем наш middleware
'admin' => \App\Http\Middleware\AdminMiddleware::class

// после этого в роутах можно указывать middleware,и сначало выполниться он  (точнее его метод handle),а затем роут(так можно перекрывать роуты админа от пользователей)

// пример скрытие админки
public function handle($request,Closure $next)
{
    if(Auth::check() && Auth::user()->is_admin){
        return $next($request);
    }
    abort(404);  //выкинет стр 404(которая предварительно создана по маршруту recources->views->errors->404.blade.php)
}

// из под коробки уже есть реализованые middleware
// auth - для авторизованых пользователей
// guest - для гостей
// называеться RedirectIfAuthenticated
```
---
#### 🔹 Controller

```php
// middleware можно указывать в конструкторе контроллера чтоб перекрать контролер

public function __construct()
{
    $this->middleware('auth');
}
```
---
#### 🔹 Передача параметров

```php
// передача параметров для middleware

// в роуте прописываем
Route::put('post/{id}', function ($id) {
    //
})->middleware('role:editor')
// где 'role' - зарегист. мидл.,а 'editor' - передоваемый параметр

// в мидл.
public function handle($request, Closure $next, $role)
{
    if (! $request->user()->hasRole($role)) {
        // Redirect...
    }

    return $next($request);
}
```
---
#### 🔹 Для конкретного маршрута

> если нужно применить к определеному маршруту можно передать его прямо в маршруте без регестрации в кернеле

```php
Route::get('posts', PostController::class)
    ->middleware(['auth', CustomMiddleware::class])
```
---


