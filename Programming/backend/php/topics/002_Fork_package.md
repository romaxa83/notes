Если в проекте используется сторонняя библиотека и в самой библиотеке нужно поправить некий функционал, то имеет место создать fork библиотеки для правки и интегрировать её на packagist с теми же Namespace. Далее пошаговая инструкция


#### Создание форка на GitHub
 
1. Найдите оригинальный репозиторий библиотеки на GitHub
2. Нажмите кнопку "Fork" в правом верхнем углу страницы
3. Выберите свой аккаунт, куда будет скопирован форк

#### Клонирование форка локально
git clone https://github.com/ваш-аккаунт/laravel-gmail.git  
cd laravel-gmail

#### Внесение изменений

## Изменение composer.json
Вам нужно изменить в вашем форке, но сохранить пространство имён оригинальной библиотеки. Отредактируйте файл следующим образом: `composer.json`

1. Измените имя пакета на свое (например, с вашим username):

"name": "ваш-username/laravel-gmail",

2. Обновите описание, чтобы указать, что это форк:

"description": "Fork of dacastro4/laravel-gmail with bug fixes for handling exceptions in batchRequest",

3. Добавьте секцию replace, чтобы указать, что ваш пакет заменяет оригинальный:

"replace": {  
    "dacastro4/laravel-gmail": "self.version"  
},

4. Обновите версию пакета, увеличив патч-версию (например, если оригинал был 6.0.0, ваш будет 6.0.1)



## 3. Внесение изменений

Найдите файл с классом `Message` и внесите необходимые изменения, добавив проверку типа:

```php

public function batchRequest($allMessages)  
{  
    $this->client->setUseBatch(true);  
  
    $batch = $this->service->createBatch();  
  
    foreach ($allMessages as $key => $message) {  
        // Пропускаем сообщение, если это исключение или неверный тип  
        if ($message instanceof Google\Service\Exception || !method_exists($message, 'getId')) {  
            continue;  
        }  
          
        $batch->add($this->getRequest($message->getId()), $key);  
    }  
  
    $messagesBatch = $batch->execute();  
  
    $this->client->setUseBatch(false);  
  
    $messages = [];  
  
    foreach ($messagesBatch as $message) {  
        // Дополнительная проверка и здесь  
        if ($message instanceof Google\Service\Exception) {  
            continue;  
        }  
          
        $messages[] = new Mail($message, false, $this->client->userId);  
    }  
  
    return $messages;  
}
```

## 4. Изменение composer.json

Вам нужно изменить `composer.json` в вашем форке, но сохранить пространство имён оригинальной библиотеки. Отредактируйте файл следующим образом:

1. Измените имя пакета на свое (например, с вашим username):

"name": "ваш-username/laravel-gmail",

2. Обновите описание, чтобы указать, что это форк:

"description": "Fork of dacastro4/laravel-gmail with bug fixes for handling exceptions in batchRequest",

3. Добавьте секцию replace, чтобы указать, что ваш пакет заменяет оригинальный:

"replace": {  
    "dacastro4/laravel-gmail": "self.version"  
},

4. Обновите версию пакета, увеличив патч-версию (например, если оригинал был 6.0.0, ваш будет 6.0.1)

## 5. Коммит и пуш изменений

git add .  
git commit -m "Fix exception handling in batchRequest method"  
git push origin main

## 6. Создание релиза на GitHub

1. Перейдите на страницу вашего форка на GitHub
2. Перейдите в раздел "Releases"
3. Нажмите "Create a new release"
4. Укажите тег версии (например, v6.0.1)
5. Добавьте заголовок и описание релиза
6. Опубликуйте релиз

## 7. Регистрация на Packagist

1. Создайте аккаунт на [Packagist.org](https://packagist.org/), если у вас его еще нет
2. Нажмите "Submit" в верхнем меню
3. Введите URL вашего GitHub репозитория
4. Следуйте инструкциям, чтобы завершить публикацию

## 8. Настройка автоматического обновления пакета

1. Перейдите в настройки вашего форка на GitHub
2. Выберите "Webhooks" в левом меню
3. Добавьте новый webhook с URL `https://packagist.org/api/github?username=ваш-packagist-username`
4. Укажите Packagist API token, который можно найти в настройках вашего аккаунта на Packagist

## 9. Использование вашего форка в проекте

Теперь вы можете использовать ваш форк в проекте, изменив `composer.json`:

"require": {  
    "ваш-username/laravel-gmail": "^6.0.1"  
},

И выполнив:

composer update ваш-username/laravel-gmail
composer update romaxa83/laravel-gmail

Поскольку вы добавили директиву `replace` в `composer.json`, ваш пакет будет использоваться вместо оригинального, сохраняя те же пространства имен (`Dacastro4\LaravelGmail`). Это означает, что вам не нужно менять код в вашем приложении — все будет работать как прежде, но с вашими исправлениями.

## Важные замечания

1. Убедитесь, что вы не нарушаете лицензию оригинального пакета. Обычно требуется указать оригинального автора и лицензию.
    
2. По возможности, создайте Pull Request к оригинальному репозиторию, чтобы ваши исправления могли быть включены в основную версию.
    
3. Будьте готовы поддерживать свой форк в актуальном состоянии, интегрируя изменения из оригинального репозитория.
    
4. Рекомендуется указать в README информацию о том, что это форк, и какие изменения были внесены.