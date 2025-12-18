#php #laravel #api-docs

- Пример документации для 3.0 - https://blog.quickadminpanel.com/laravel-api-documentation-with-openapiswagger/

#### 🔹 Install

 - https://github.com/DarkaOnLine/L5-Swagger

```bash
# ставим пакеты
composer require darkaonline/l5-swagger
composer require zircote/swagger-php

# публикуем конфигурацию
php artisan vendor:publish --provider "L5Swagger\L5SwaggerServiceProvider"

# генерируем документацию (котороая доступна по /api/documentation)
php artisan l5-swagger:generate

# если возникла ошибка
Symfony\Component\Debug\Exception\FatalThrowableError : Call to undefined function OpenApi\scan()
# то добавляем строку SWAGGER_VERSION=2.0 в файл .env
```
---
#### 🔹 Примеры аннотаций по darkaonline/l5-swagger Версии 3.0

##### 🔸 Базовая аннотация
```php
/**
 * @OA\Info(
 *     title="Some app api documentation",
 *     version="1.0.0",
 *     @OA\Contact(
 *         name="Rodomanov Roman",
 *         email="rodomanov.r.wezom@gmail.com"
 *     ),
 *     @OA\License(
 *         name="Apache 2.0",
 *         url="http://www.apache.org/licenses/LICENSE-2.0.html"
 *     )
 * )
 * @OA\Tag(
 *     name="User",
 *     description="Crud for user",
 * )
 * @OA\Tag(
 *     name="Product",
 *     description="Action with product ",
 * )
 * @OA\Server(
 *     description="stage server",
 *     url="https://arma-motors.wezom.agency/api/v1"
 * )
 * @OA\SecurityScheme(
 *     type="apiKey",
 *     in="header",
 *     name="Authorization",
 *     securityScheme="Basic"
 * )
 */
```

##### 🔸 Post with requestBody

> аннотация для метода (POST), где указа requestBody ввиде json ( секция описана отдельно к примеру в реквестах) а также ответы тоже ввиде json (секции описаны отдельно)
```
/**
 * @OA\Post (
 *     path="api/users",
 *     tags={"User"},
 *     security={
 *       {"Basic": {}},
 *     },
 *     summary="Create user",
 *     @OA\RequestBody(required=true, @OA\JsonContent(ref="#/components/schemas/CreateRequest")),
 *
 *     @OA\Response(response="200", description="OK", @OA\JsonContent(ref="#/components/schemas/UserResorce")),
 *     @OA\Response(response="400", description="Bad Request", @OA\JsonContent(ref="#/components/schemas/ErrorResponse")),
 *     @OA\Response(response="500", description="Server Error", @OA\JsonContent(ref="#/components/schemas/ErrorResponse")),
 * )
 */
```

-----------------------------------------------------
##### 🔸 Post with requestBody and Response
```php
/**
 * @OA\Post (
 *     path="/mobile/sms-verify/check",
 *     tags={"SMS"},
 *
 *     summary="Check sms",
 *     @OA\RequestBody(required=true, @OA\JsonContent(ref="#/components/schemas/SmsCheckRequest")),
 *
 *     @OA\Response(response="200", description="OK",
 *          @OA\JsonContent(
 *              @OA\Property(property="data", title="Data", type="object",
 *                   @OA\Property(property="actionToken", title="Action token", example="7b11027f-1913-411a-b5ec-8878ef3a7c30")
 *              )
 *         ),
 *         @OA\Property(property="success", title="Success", example=true),
 *     ),
 *     @OA\Response(response="400", description="Bad Request", @OA\JsonContent(ref="#/components/schemas/ErrorResponse")),
 *     @OA\Response(response="500", description="Server Error", @OA\JsonContent(ref="#/components/schemas/ErrorResponse")),
 * )
 */
 
 
// с вложенным ответом ввиде обьекта
/**
*     @OA\Response(response="200", description="OK",
*          @OA\JsonContent(
*              @OA\Property(property="data", title="data", type="object",
*                  ref="#/components/schemas/ReviewResource"
*              )
*          )
*     ),
```

-----------------------------------------------------
##### 🔸 For Request

> аннотации описанные в реквесте (или в классе отвечающий за пришедшии данные), данный тип аннотаций можно применять и для ответов

```php
// перед обьявление класса прописываем
/**
 * @OA\Schema(
 *     type="object",
 *     title="Request for create user",
 *     required={"phone", "name", "email"}
 * )
 */
 
 // описываем сами поля
 
/**
 * @OA\Property(property="name", title="Name", description="Имя пользователя", example="Иван")
 * @OA\Property(property="surname", title="Surname", description="Фамилия пользователя", example="Иванов")
 * @OA\Property(property="email", title="Email", description="Email", example="ivan@gmail.com")
 * @OA\Property(property="phone", title="Phone", description="Телефон", example="380954545667")
 * @OA\Property(property="lang", title="Language", description="Локаль для пользователя", example="ru")
 */
```

==пример аннотации с сложеным обьектом, такого формата==
```json
{
	"data": {
		"id": 1,
		"name": "some name"
	}
}
```

```php
/**
 *  @OA\Property(property="data", title="Data", type="object",
 *     @OA\Property(property="id", title="ID", description="ID пользователя", example=1),
 *     @OA\Property(property="name", title="Name", description="Имя пользователя", example="Иван")
 * )
 */
```


==пример аннотации с сложеным массивом, такого формата==
```json
{
	"id": "1",
	"plannig": [
		{
			"startDate": "2021-09-01T16:53:40",
			"endDate": "2021-09-01T16:53:40"
		}
	]
}
```

```php
/**
 * @OA\Property(property="id", title="ID", description="ID заявки", example="10266ab2-7cec-11ec-8277-4cd98fc26f14")
 * @OA\Property(property="planning", title="Planing", type="array",  @OA\Items(
 *      @OA\Property(property="startDate", title="Start date", description="Дата начало заявки", example="2021-09-01T16:53:40"),
 *      @OA\Property(property="endDate", title="End date", description="Дата конца заявки", example="2021-09-01T16:53:40")
 *  ))
 */
```
---
##### 🔸 Header in Response
```php
//указать в респонсе Hesader
/**
* @OA\Response(response="200", description="OK",
*     @OA\JsonContent(ref="#/components/schemas/TokensResponse"),
*     @OA\Header(header="x-next", @OA\Schema(type="string"), description="A link to the next page of responses")
* ), 
```
-----------------------------------------------------
##### 🔸Array
```php
// анотация для многомерного массива
@OA\Property(property="planning", title="Planing", type="array",  @OA\Items(type="array", @OA\Items()))
```
---
