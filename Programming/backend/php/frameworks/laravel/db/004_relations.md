#php #laravel #database 

#### 🔹 ONE TO ONE

==на примере user <-> profile==
```php
class User extends Model
{
    public function profile()
    {
        return $this->hasOne(Profile::class);
    }
}
class Profile extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

> `belongsTo` - используется, в модели где есть `id` ссылающийся на другую модель в данном примере `profile` имеет `user_id`

```php
// сохранение
$profile = new Profile();
$profile->birthday = '20-03-1999';

$user = User::find(1);
$user->profile()->save($profile);

// обновить
$user = User::first();
$user->name = "Peter";
$user->profile->birthday = '1234567890';
$user->push(); // Это обновит и пользователя и профиль в базе данных

// удаление
$user = User::find(1);
$user->profile()->delete();
```

> чтоб при удалении родительской модели удалялась и дочерняя , в миграциях, при описание связи используем `onDelete('cascade')` 

```php
// деталшьное указание для связей
$this->belongsTo(
    User::class,    // модель с которой идет связь
    'user_id',      // ключ данной модели
    'id'            // ключ для связаной модели
);
```
---
#### 🔹 ONE TO MANY

==примере product <<-> brand==
```php
class Brand extends Model
{
    public function products()
    {
      return $this->hasMany(Product::class);
    }
}
class Product extends Model
{
    public function brand()
    {
      return $this->belongsTo(Brand::class);
    }
}
 
// сохранение
$brand = Brand::find(1);

$product = new Product();
$product->name = 'iPhone X';

$brand->products()->save($product);
```
---
#### 🔹 MANY TO MANY

==пример article <<->> tag используется сводная таблица (pivot) с полями article_id, tag_id==
```php
class Article extends Model
{
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
}

class Tag extends Model
{
    public function articles()
    {
        return $this->belongsToMany(Article::class);
    }
}

// сохранение (привязывание)
$article = Article::create([
    'title'  =>  'some title'
]);

$tags = Tag::find([2,3]);

// можно передавать как обьекты так и массив id
$article->tags()->attach($tags);

// если в сводной (pivot) таблице есть доп. поля
$article->tags()->attach($tags, [
    'views' => 100
]);

// удаление (отвязывание)
$article->tags()->detach([1,2]);
// если в detach ничего не передовать, удалит все привязаные элементы
$article->tags()->detach();
```

```php
// указывание полей для связывания
public function tags()
{
    return $this->belongsToMany(
        Article::class,     // модель для связывания
        'article_tag',      // название pivot таблицы
        'article_id',          // поле в pivotTable относящей к данной модели
        'tag_id'         // поле в pivotTable относящей к связаной модели
    );
}
```

```php
// получение данных из доп. поле pivotTable
public function tags()
{
    return $this->belongsToMany(Article::class)
        ->withPivot('views', 'likes');
}
```

```php
// если в pivotTable добавлены веременые метки получить их можно так
public function tags()
{
    return $this->belongsToMany(Article::class)->withTimestamps();
}
```

```php
// если нужна сортировка по дополнительному полю из
// pivot таблицы, к примеру, помимо 'article_id' и 'tag_id'
// есть еще поле 'sort', по нему нужно сортировать

return $this->belongsToMany(Article::class)
            ->withPivot('sort')
            ->orderBy('sort');
```
---
#### 🔹 MANY TO MANY POLYMORPHIC

> яркий пример это таблицы картинок (или комментариев) которые могут иметь связь с любыми моделями

> реализация в таблице `image` создаем поля
- `imageable_id - integer`
- `imageable_type - string`

```php
class Image extends Model
{
    public function imageable()
    {
        return $this->morphTo();
    }
}

# в модели прописываем связь на картинки

public function images()
{
    return $this->morphMany(Image::class, 'imageable');
}
```
---
#### 🔹 HAS ONE THROUGH
- https://stitcher.io/blog/laravel-has-many-through

```bash
# к примеру есть такая структура

Supplier (Поставщик)
- id

User
- id
- supplier_id

History
- id
- user_id
  
# суть связи в том что нам нужно из supplier получить историю пользователя (через таблицу пользователя) через данную связь будет получена одна запись (history)
```

```php
class Supplier extends Model
{
    public function history()
    {
        return $this->hasOneThrough(
          History::class,   // с какой моделью связываемся  
          User::class       // промежуточная модель
        );
    }
}

// описание аргументов для связывания
return $this->hasOneThrough(
  History::class,
  User::class,
  'supplier_id',  // ключ в промежуточной таблице для связи с текущей моделью
  'user_id',      // ключ в связываемой таблице , которая связана с промежуточной
  'id',           // ключ для связи текущей модели
  'id'            // ключ для связи промежуточной модели с связываемой
);
```
---
#### 🔹 HAS MANY THROUGH

> анологична по реализации с `hasOneThrough` только вытащит все записи истории по всем пользователям которые привязаны к конкретному supplier

---
#### 🔹 WITH DEFAULT

> чтоб избежать фатальной ошибки при запросе поле в отсутствующих отношениях `($order->user->name)` используй `withDefault()` вернет пустую модель

```php
public function user()
{   
    return $this->belongsTo('App\User')->withDefault();
}

# можно задать дефолтные значения для полей

public function user()
{   
    return $this->belongsTo('App\User')
        ->withDefault(['name' => 'Anonymous']);
}
```
---
#### 🔹 WITH COUNT

> `WithCount()` (для подсчета дочерних записей)

> если есть отношение `hasMany()` и нужно подсчитать количество «дочерних» записей, не нужно писать дополнительный запрос.
   Например, если у вас есть записи и комментарии в модели `User`, сделайте с помощью `withCount()`:

```php
public function index()
{
    $users = User::withCount(['posts', 'comments'])->get();
    return view('users', compact('users'));
}

// доступ к каунтам
@foreach ($users as $user)
    <tr>
        <td>{{ $user->name }}</td>
        <td class="text-center">{{ $user->posts_count }}</td>
        <td class="text-center">{{ $user->comments_count }}</td>
    </tr>
@endforeach
```
---
#### 🔹 ORDER BY

```php
// сортировка в отношениях
public function products()
{
    return $this->hasMany(Product::class);
}
public function productsByName()
{
    return $this->hasMany(Product::class)->orderBy('name');
}
```
---
#### 🔹 PARENT_ID

> если в одно таблице есть элементы ссылающиеся на элемент в этой таблице (к примеру категории и под категории)

```php
// в модели создаем связи
class Category extends Model
{
    public function categories()
    {
        return $this->hasMany(Category::class);
    }

    public function childrenCategories()
    {
        return $this->hasMany(Category::class)->with('categories');
    }
}
// если вызовем Category::with(‘childrenCategories’), то 
// получим все уровни (рекурсивно)

// пример запроса
$categories = Category::whereNull('category_id')
    ->with('childrenCategories')
    ->get();

```
---


