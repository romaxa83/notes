#php #laravel 

> Используем вариант с добавление доп. поля в таблицу `user` (чтоб не делать доп. запрос в бд, если выносить в отдельную таблицу)

```php
// добавляем текстовое поле (или json)
$table -> text("settings");

// в модель добавляем преобразование
protected $casts = ["settings" => "array"];

// метод для получение настройки по имени, 
// если такого нет , применяеться дефолтное значение
public function setting(string $name, $default = null)
{
    if (array_key_exists($name, $this->settings)) {
        return $this->settings[$name];
    }    
    return $default;
}

// обновление одной или нескольких настроек
public function settings(array $revisions, bool $save = true) : self
{
    $this->settings = array_merge($this->settings, $revisions);    
    if ($save) {
        $this->save();
    }   
    return $this;
}
// параметр save - добавляеться если не нужно сразу сохранять
// сразу , т.к. над пользаователем будут проводиться манипуляции
// и сохраниться он поже
```
---
#### 🔹 Пример тестов

```php
/** @test */
public function a_user_can_get_a_setting()
{
    $settings = ["settings" => ["foo" => "bar"]];
    $user = factory(User::class, 1)->create($settings);

    $this->assertEquals("bar", $user->setting("foo"));
    $this->assertNull($user->setting("baz"));
    $this->assertEquals(5, $user->setting("baz", 5));
}

/** @test */
public function a_user_can_change_settings()
{
    $settings = ["settings" => ["foo" => "bar"]];
    $user = factory(User::class, 1)->create($settings);
    $this->assertEquals(
        "world", 
        $user->settings(["foo" => "world"], false)->setting("foo")
    );
    $this->assertEquals(
        "hello", 
        $user->settings(["baz" => "hello"], false)->setting("baz")
    );
    $this->assertEquals(
        ["foo" => "bar"], $user->refresh()->settings
    );
}

/** @test */
public function a_user_can_change_and_save_settings()
{
    $settings = ["settings" => ["foo" => "bar"]];
    $user = factory(User::class, 1)->create($settings);
    $this->assertEquals(
        "world", 
        $user->settings(["foo" => "world"])->setting("foo")
    );
    $this->assertEquals(
        ["foo" => "world"], $user->refresh()->settings
    );
}
```
---