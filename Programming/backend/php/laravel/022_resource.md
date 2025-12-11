#php #laravel 
#### 🔹 Практический пример ResourceCollection

> Если нужно в ресурс, где одним из полей является коллекция, в эту коллекцию, для каждого элемента добавить дополнительные данные, к примеру - есть ресурс "категория" и в ней есть коллекция товаров, и этим товарам нужно дополнительно передать, какие-то данные о категории, то реализуем так, нужно создать кастомный класс `ProductResourceCollection extends ResourceCollection` и прокидывайте свойства на каждый элемент

```php
use Illuminate\Http\Resources\Json\ResourceCollection;

class ProductResourceCollection extends ResourceCollection
{
    protected $categoryColor = '';

    public function setCategoryColor($categoryColor) {
        $this->categoryColor = $categoryColor;
        return $this;
    }

    public function toArray($request)
    {
        $this->collection->each->setCategoryColor($this->categoryColor);
        return parent::toArray($request);
    }
}
```

==это добавляем в ресурс продукта==
```php
use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class ProductResource extends JsonResource
{
    protected $categoryColor = '';

    public function setCategoryColor($categoryColor) {
        $this->categoryColor = $categoryColor;
        return $this;
    }

    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'color' => $this->categoryColor,
        ];
    }

    public static function collection($resource)
    {
        return new ProductResourceCollection($resource);
    }
}
```

==и сам проброс==
```php
use Illuminate\Http\Resources\Json\JsonResource;

class CategoryResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'product' => ProductResource::collection($this->products)
                ->setCategoryColor($this->color)
        ];
    }
}
```
-----------------------------------------------------
