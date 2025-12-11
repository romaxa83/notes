#php #laravel #package #auth 

#### 🔹 Simple flow

```bash
composer require laravel/passport
php artisan migrate

# генерит секретные ключи 
php artisan passport:install
# OR
# генерим ключ для пользователя
php artisan passport:client --password --provider=users --name='Users';
```

```php
// добавляем трейт в модель User
use Laravel\Passport\HasApiTokens;


// в AuthServiceProvider в методе boot, регистрируем
// роуты и время жизни токенов

public function boot()
{
    $this->registerPolicies();

    Passport::routes();

    Passport::tokensExpireIn(
        now()->addMinutes(config('auth.oauth_tokens_expire_in'))
    );
    Passport::refreshTokensExpireIn(
        now()->addMinutes(config('auth.oauth_refresh_tokens_expire_in'))
    );
    Passport::personalAccessTokensExpireIn(
        now()->addMinutes(config('auth.oauth_personal_access_tokens_expire_in'))
    );
}
```

==для убобства время жизни токенов выносим в конфиг==
```php
'oauth_tokens_expire_in' => env('ACCESS_TOKEN_LIFETIME', 6000),
'oauth_refresh_tokens_expire_in' => env('REFRESH_TOKEN_LIFETIME', 12000),
'oauth_personal_access_tokens_expire_in' => env('PERSONAL_ACCESS_TOKENS_EXPIRE_IN', 525600),

// добавляем в config/auth.php в массив в guard, вторым элементом после web
'api' => [
    'driver' => 'passport',
    'provider' => 'users',
    'hash' => false,
],
```
---
