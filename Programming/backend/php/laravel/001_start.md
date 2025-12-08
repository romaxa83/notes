#php #laravel 

#### 🔹 INSTALL
```bash
composer create-project --prefer-dist laravel/laravel <app_name>

# копируем файл переменых окружения, и заполняем в коде доступ через env('назв.пере','default')
cp .env.example .env

composer install

# установка пакетов
composer require --dev barryvdh/laravel-debugbar
composer require --dev barryvdh/laravel-ide-helper

# задать права папкам
sudo chmod 777 -R storage
sudo chmod 777 -R public
```
---
#### 🔹 Articles

- https://laravel.demiart.ru/
- https://badcode.ru/tag/laravel/
- https://unetway.com/tutorial/laravel-ustanovka
- https://laravelnews.ru/category/articles
- https://adminnotes.info/category/laravel
- https://web-programming.com.ua/category/laravel/
- https://pacificsky.ru/frameworks/laravel/

==socket.io==
- https://badcode.ru/laravel-socket-io-broadcasting/
- https://codecourse.com/watch/up-and-running-with-laravel-websockets?part=firing-an-event-up-and-running-with-laravel-websockets

 =="Архитектура сложных веб-приложений. С примерами на Laravel"==
- https://github.com/adelf/acwa_book_ru
---
#### 🔹 ОПТИМИЗАЦИЯ
```bash
# для оптимизации можно кешировать конфиги
php artisan config:cache

# но при этом внесенные изменения в конфиги не будут работать пока кеш не зброситься
php artisan config:clear

# кеширование путей
php artisan route:cache
# зброс кеша для роутов
php artisan route:clear

# чтобы убрать включения (include) и создать один файл выполните следующую команду
php artisan optimize –force
```
---
#### 🔹 JETSREAM
```bash
# ставим пакет
composer require laravel/jetstream

# запускаем сборку (inertia + vue)
php artisan jetstream:install inertia

# запустить сборку
npm i && npm run dev

# при разработке
npm run watch
```
---
#### 🔹 Загрузка js-библеотек
```bash
npm install -g yarn
yarn install
yarn run dev # (coздает не минифицированые файла js и css)
# на проде стоит запускать yarn run prod ,чтоб их минифицировать
```

в файле `webpack.mix.js` (который отвечает за сборку) добавляем в `mix`
```js
mix.setPublicRath('public/build')    //папка в которую будут билдиться файлы
   .setResourceRoot('build')         //будет добавлено к относит. путям,для корректной работы
   .js('resources/assets/js/app.js','js')
   .sass('resources/assets/sass/app.scss','css')
   .version(); 
   // добавляет к файлам id ,чтоб они при изменениях перекешировались
// после этого можно удалить в public папку js и css,
// а также файл mix-manifest.json,
// а саму папку добавить в .gitignore
// также в app -> resources -> views -> layouts -> app.blade.php {к подключаемым // файлам
// js и css меняем href на}->{{ mix('css/app.css','build') }} {он подключит эти файлы
// из mix-manifest.json , где после сборки сгенерируються id к ним}
```
---
#### 🔹 У даляем заглушку и делаем новую стартовую страницу
- recources -> views {удаляем}->welcome.blade.php
- routes -> web {удаляем верхний роут и вставляем}->Route::get('/home', 'HomeController@index')->name('home');
- app -> http -> controllers -> auth -> loginController {меняем redirectTo}->'/cabinet'
- app -> http -> controllers {создаем папку}->Cabinet {в ней создаем}->HomeController  {в него копируем все из HomeController, меняя view}->cabinet.home
- recources -> views {создаем папку} ->cabinet {в ней создае файл} ->home.blade.php {который будет рендериться когда мы залогинимся}
- recources -> views -> layouts -> app.blade.php {указываем ссылку на кабинет перед logout вставляем}-> <a class="nav-link" href="{{ route('cabinet') }}">Cabinet</a>
- routes -> web {после Auth::routes , вставляем} -> Route::get('/cabinet', 'Cabinet\HomeController@index')->name('cabinet');
- app -> http ->controllers ->HomeCoontroller.php {удаляем конструктор с auth}
----
#### 🔹 Для большей безапастности
в файле app -> Http -> Kernel.php
в методе $middlewareGroups раскомментируем закоментировану строчку
если это сделать то при смене пользователем пароля ,у него автоматически
произойдет логаут во всех устройствах

---
#### 🔹 LARAVEL 11

// Работа с новой архитектурой в Laravel 11
https://habr.com/ru/articles/822185/
---












