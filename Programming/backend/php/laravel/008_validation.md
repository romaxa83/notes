#php #laravel #validation

 - https://laravel.com/docs/8.x/validation

#### 🔹 Email
```php
// обязательно, формат email, уникально в таблице users
'email' => 'required|email|unique:users'

// если обновляеться пользователь то его email уже есть в бд,
// и поэтому вылезет ошибка валидации,чтоб устранить используем правила
use Illuminate\Validation\Rule;

'email' => [
	'required',
	'email',
	Rule::unique('users')->ignore($user->id),
];
```
---
#### 🔹 Required_if

```php
// goods - обязательно если type_loan = 1
'type_loan' => ['required', 'string', 'between:0,1'],
'goods' => ['required_if:type_loan,1']

```
---
#### 🔹 IMAGE

```php
'files.*' => 'required|image|mimes:jpg,jpeg,png'

// можно указывать требуемые размеры
'photo' => 'dimensions:max_width=4096,max_height=4096'
```
---
#### 🔹 DIFFERENT

```php
// sub_manager_id должно отличаться от manager_id
'manager_id' => ['required','integer'],
'sub_manager_id' => ['nullable' ,'integer','different:manager_id']
```
---
#### 🔹 EXISTS

```php
// Проверяет на существование в с таблице
// чтоб данное значение было в таблице car_models в поле id
'model_id' => ['required', 'integer', 'exists:car_models,id'];

```
---
#### 🔹 Валидация в REQUEST

Валидацию можно вынести в Request:
- создаем класс который `extends FormRequest implements RequestInterface`
- в нем нужно реализовать два обязательных метода 

```php
// нужно вернуть true чтоб пройти дальше
public function authorize(): bool
{
	//return $this->user()->can('create.posts');
    return true;
}

// правила валидации
public function rules(): array
{
    return [
        'user_id' => ['required', 'integer'],
    ];
}

// если метод authorize вернет false, то получите ошибку 403
// ее можно перехватить в методе render
// public function render($request, Exception $exception)
{
   if ($exception instanceof \Illuminate\Auth\Access\AuthorizationException) {
      //
   }
   return parent::render($request, $exception);
}

// также можно реализовать метод message

public function messages()
{
    return [
        'title.required' => 'Требуется Заголовок.',
        'title.unique' => 'Заголовок сообщения уже существует.',
        ...
    ];
}

// который будет возвращать сообщения на ошибки валидации
// на фронте их можно отлавливать
@if ($errors->any())
   @foreach ($errors->all() as $error)
      {{ $error }}
   @endforeach
@endif
```
---
#### 🔹 Валидация через REQUEST

```php
public function store(Request $request)
{
    $validated = $request->validate([
        'title' => 'required|unique:posts|max:255',
    ]);
}

// если не пройдет в этом методе будет false
$validator->fails();
```
---
#### 🔹 Валидация через VALIDATOR

```php
$validator = Validator::make($data, $rule);

// здесь ошибки
$validator->errors();
```
---
#### 🔹 Валидация даты

 > Можно провести валидацию дат правилами до и после (`before/after`) и задавать различные параметр, например: «tomorrow» (завтра), «now» (сегодня), «yesterday» (вчера). 
   Например: `‘start_date’ => ‘after:now’`. Под капотом у этого используется функция `strtotime()`.

```php
$rules = [
    'start_date' => 'after:tomorrow',
    'end_date' => 'after:start_date'
];
```
---
#### 🔹 Валидация файлов

```php
'video' => 'max:1000000|required|file|mimetypes:video/mp4,video/mpeg,video/x-matroska',
```
---
#### 🔹 Bail

> Если нужно остановить валидацию после первой же ошибки, то используйте правило `bail` в начале массива правил. В приведенном примере, если поле `title` будет пустое, то мы сэкономим один запрос в базу данных.

```php
'title' => ['bail', 'required', 'unique:posts']
```
---
#### 🔹 Кинуть ошибку валидации

```php
throw ValidationException::withMessages(['id' => __("exceptions.gps_device.has_attached_vehicle")], 422);
```
---
#### 🔹 RequiredUnless

> Если нужно чтоб определенное поле было обязательно, только в случае если другое поле отсутствует или равно `null`

```php
'first_name' => [
    Rule::requiredUnless($this->filled('customer_address_id')), 
    'string', 
    'alpha', 
    'max:191'
],
```
---
#### 🔹 RequiredIf

```php
// Если `customer_address_id` отсутствует или его значение равно `null`, указанные поля становятся обязательными.

'delivery_address' => ['sometimes', 'array'],
'delivery_address.customer_address_id' => ['sometimes', 'int', Rule::exists(Address::TABLE, 'id')],
'delivery_address.first_name' => [
    Rule::requiredIf(function () {
        return $this->input('delivery_address.customer_address_id') === null;
    }),
    'string',
    'alpha',
    'max:191'
],
'delivery_address.last_name' => [
    Rule::requiredIf(function () {
        return $this->input('delivery_address.customer_address_id') === null;
    }),
    'string',
    'alpha',
    'max:191'
],
```
---




