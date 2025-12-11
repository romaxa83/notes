#php #laravel #testing 

#### 🔹 DATA PROVIDER (с использованием замыкания)

```php
/**
 * @test
 * @dataProvider requests
 */
public function ignore_query_if_wrong(\Closure $sendRequest, $count)
{
    ....

    /** @var $res TestResponse */
    $res = $sendRequest->call($this);
    $res->assertJson([
        "meta" => [
            "total" => $count,
        ]
    ])
        ->assertJsonCount($count, 'data')
    ;
}

public function requests(): array
{
    return [
        [
            function (){
                return $this->getJson(route('api.reports', ['model_description_id' => 'null']));
            },
            2
        ],
        [
            function (){
                return $this->getJson(route('api.reports', ['model_description_id' => null]));
            },
            2
        ],
    ];
}
```
---
#### 🔹 Пример по использованию для валидации

```php
/**
 * @test
 * @dataProvider validate
 */
public function validate_main_data($field, $value, $msg)
{
    ....

    $this->postJson(route('api.report.create'), [
        $field => $value
    ])
        ->assertJson($this->structureErrorResponse([$msg]))
    ;
}

public function validate(): array
{
    return [
        ['salesman_name', 99999, 'The salesman_name must be a string.'],
        ['machine_for_compare', 99999, 'The machine for compare must be a string.'],
        ['assignment', 99, 'The assignment must be a string.']
    ];
}
```
---
