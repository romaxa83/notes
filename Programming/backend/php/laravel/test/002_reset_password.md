#php #laravel #testing

#### 🔹 Basic use

> Для тестирования функциональности пароля reset нужно заменить сгенерированный токен из таблицы `password_reset` на новый.

> Токен reset создается с помощью метода `createTokenRepository() -laravel/framework/src/Illuminate/Auth/Passwords/PasswordBrokerManager.php`

> Для хэширования созданного токена Laravel использует метод `make() - laravel/framework/src/Illuminate/Hashing/BcryptHasher.php`

==пример==
```php
public function test_it_should_reset_the_password()
{

    Mail::fake();

    $user = factory(App\User::class)->create();

    $response = $this->json('POST', 'api/password/email',
                    [
                        'email' => $user->email
                    ]);
    $response->assertStatus(202);

    Mail::hasSent($user, ResetPassword::class);

    // Since we don't know the emailed token from 
    // the previous JSON call, we're
    // gonna replace the token with a new one
    $token = hash_hmac('sha256', Str::random(40), $user);
    DB::table('password_resets')
            ->where('email', $user->email)
            ->update([
                'token' => password_hash($token, PASSWORD_BCRYPT, ['cost' => '10'])
            ]);

    $response = $this->json('POST', 'api/password/reset', [
                    'email'                 => $user->email,
                    'password'              => 'new_user_password',
                    'password_confirmation' => 'new_user_password',
                    'token'                 => $token
                ]);
    $response->assertStatus(202);

    $response = $this->json('POST', 'api/login',
                    [
                        'email' => $user->email,
                        'password' => 'new_user_password'
                    ]);
    $response->assertStatus(202);
    // check for JWT token
    $response->assertJson(['token' => true]);
}
```
---