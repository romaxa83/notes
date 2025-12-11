#php #laravel #code 

#### 🔹 AbstractRepository

```php
<?php  
  
namespace App\Repositories;  
  
use Illuminate\Contracts\Pagination\LengthAwarePaginator;  
use Illuminate\Database\Eloquent\Builder;  
use Illuminate\Database\Eloquent\Collection;  
use Illuminate\Database\Eloquent\Model;  
use Illuminate\Http\Response;  
  
abstract class AbstractRepository  
{  
    const DEFAULT_LIMIT = 10;  
  
    protected Model $model;  
  
    public function __construct()  
    {  
        $this->model = app($this->modelClass());  
    }  
  
    abstract protected function modelClass(): string;  
  
    public function getQuery(): Builder  
    {  
        return $this->model::query();  
    }  
  
    public function getAllQuery(array $relations = [], $sortField = 'id', $sort = 'asc'): Builder  
    {  
        return $this->getQuery()->with($relations)->orderBy($sortField, $sort);  
    }  
  
    public function getAll(array $relations = [], $sortField = 'id', $sort = 'asc'): Collection  
    {  
        return $this->getAllQuery($relations, $sortField, $sort)->get();  
    }  
  
    public function getAllBy(  
        string $field,  
        string $value,  
        array $relations = [],  
        $sortField = 'id',  
        $sort = 'asc'  
    ): Collection  
    {  
        return $this->getQuery()  
            ->where($field, $value)  
            ->with($relations)  
            ->orderBy($sortField, $sort)  
            ->get();  
    }  
  
    public function getAllPaginator(array $params, array $relations = [], $sortField = 'id', $sort = 'asc'): LengthAwarePaginator  
    {  
        $perPage = $params['perPage'] ?? self::DEFAULT_LIMIT;  
  
        return $this->getAllQuery($relations, $sortField, $sort)->paginate($perPage);  
    }  
  
    public function getAllWithCount(array $relations = []): Collection  
    {  
        return $this->getQuery()->withCount($relations)->get();  
    }  
  
    public function getForSelect($field, array $relations = []): array  
    {  
        return $this->getAllQuery($relations)->pluck($field, 'id')->toArray();  
    }  
  
    /*  
     *  Get one model     */  
    public function getOneQuery(  
        string $field,  
        string $value,  
        array $relations = []  
    ): Builder  
    {  
        return $this->getQuery()  
            ->with($relations)  
            ->where($field, $value);  
    }  
  
    public function getOneBy(  
        string $field,  
        string $value,  
        array $relations = []  
    ): ?Model  
    {  
        return $this->getOneQuery($field, $value, $relations)->first();  
    }  
  
    public function findOneBy(  
        string $field,  
        string $value,  
        array $relations = []  
    ): Model  
    {  
        if($model = $this->getOneBy($field, $value, $relations)){  
            return $model;  
        }  
  
        throw new \DomainException("Not found model", Response::HTTP_NOT_FOUND);  
    }  
}
```
---
#### 🔹 route.api

```php
<?php  
  
use Illuminate\Support\Facades\Route;  
use \App\Http\Controllers\Api;  
  
Route::get('info', Api\InfoController::class)  
    ->name('api.info');  
  
// Profile  
Route::prefix('profile')->group(function(){  
  
    Route::post('login', [Api\V1\Auth\AuthController::class, 'login'])->name('api.v1.auth.login');  
    Route::post('register', [Api\V1\Auth\AuthController::class, 'register'])->name('api.v1.auth.register');  
  
    Route::group([  
        'middleware' => ['auth:api'],  
    ], function(){  
  
    });  
});
```
---
#### 🔹 ApiController

```php
<?php  
  
namespace App\Http\Controllers\Api;  
  
use App\Http\Controllers\Controller;  
use Illuminate\Http\Response;  
  
class ApiController extends Controller  
{  
    protected function successJsonMessage($message, $code = Response::HTTP_OK)  
    {  
        return response()->json([  
            'data' => $message,  
            'success' => true  
        ], $code);  
    }  
  
    protected function errorJsonMessage($message, $code = Response::HTTP_INTERNAL_SERVER_ERROR)  
    {  
        $code = 0 === $code ? Response::HTTP_INTERNAL_SERVER_ERROR : $code;  
  
        return response()->json([  
            'data' => $message,  
            'success' => false  
        ], $code);  
    }  
}
```
---