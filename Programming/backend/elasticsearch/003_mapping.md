#### 🔹 MAPPING 

==Маппинг== (сопоставление) — это процесс определения схемы или структуры документов. Он описывает свойства полей в документе. Свойства поля включают тип данных (например, string, integer и т.д.) и метаданные

Схема не может быть изменена(т.е. изменить тип данных), можно добавить новые поля или использовать несколько полей для индексации одного и того же поля с использованием нескольких типов данных.

По умолчанию для любых полей, не входящих в маппиг, тип данных определяется на основе первого найденого значения поля, чтоб избежать неожиданостей это можно отключить

- `"dynamic" : false` - поля не в схеме игнорируются,
- `"dynamic" : true` - по умолчанию новые поля автоматически добавляються в схему,
- `"dynamic" : strict` - будет вызвано исключение, если вы пытаетесь проиндексировать документ с неизвестным полем

#### 🔹 Settings
Перед создание схемы можно указать настройки схемы

```json
"settings": {
    "number_of_shards": 2,          // кол-во шардов
    "number_of_replicas": 1         // кол-во реплик
}
```

#### 🔹 Пример описание документа на (продукта)

```
PUT shop
{
    "settings": {
        "number_of_shards": 2,
        "number_of_replicas": 1
    },
    "mappings": {
        "product": {
            "dynamic" : "strict",
            "properties": {
                "id" : {
                    "type": "integer"
                },
                "name": {
                    "type": "text",
                    "fields": {
                        "exact": {
                            "type": "keyword"
                        }
                    }
                },
                "category": {
                    "type": "keyword"
                },
                "brand": {
                    "type": "keyword"
                },
                "price": {
                    "type": "float"
                },
                "amount": {
                    "type":"integer"
                },
                "date": {
                    "type":"date",
                    "format": "yyyy-MM-dd"
                },
                "status": {
                    "type": "boolean"
                },
                "agent": {
                    "type": "nested",
                    "properties": {
                        "name": {
                            "type" : "text"
                        },
                        "email": {
                            "type": "keyword"
                        },
                        "company": {
                            "type": "keyword"
                        }
                    }
                },
                "description": {
                    "type": "text",
                    "analyzer": "russian"
                }
            }
        }
    }
}
```

==в ответ прийдет==
```json
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "shop"
}
```

 ==выведет схему индекса==
 ```
 GET /shop/product/_mapping
 ```


Когда нужно проиндексировать одно поле с указанием нескольких типов данных данная возможность называется `fieldsmapping`, к примеру поле title нужно проиндексировать как `text` и `keywords`, что не дублировать данные используется такая конструкция
```json
{
  "properties": {
    "title": {
      "type": "text",
      "fields": {
        "exact": {
          "type": "keyword"
        }
      }
    }
  }
}   
```
---
#### 🔹 Удалить схему

```
DELETE /shop
```
---
#### 🔹 Поле `_all`

В elastic есть поле `_all` , в котором находятся значения всех поле через пробел что позволяет производить поиск по всем полям. По умолчанию если в запросе поле не указано, подставляется `_all`

```
GET example3/history/_search?q=UNKNOWN
```

При создании индекса его можно отключить(уменьшиться размер индекса на диске)

```json
{
    "_all": {
       "enabled": false
	},
   "properties": {
     .....
   }
 }
```

==отключение определенного поля из `_all`==
```json
"properties": {
    "description" : {
        "type": "keyword"
        "include_in_all": false
    }
}
```
---
#### 🔹 Добавление нового типа/поля в индекс

```
 PUT shop/_mapping/history
 {
   "properties": {
      "ip_address": {
       "type": "ip"
     }
     "login_date": {
       "type": "date",
       "format": "yyyy-MM-dd"
     }
   }
 }
```
---
#### 🔹 СВЯЗИ МЕЖДУ ДОКУМЕНТАМИ 

есть два способа
  - отношения родитель-ребенок(parent-child)
  - вложенные документы

`Parent-child` (по сути отношения один ко многим), при индексации дочернего документа нужно указать родительский идентификатор. Используется в следующих случаях:
  - независимое обновление обоих типов документа(новый комментарий может быть добавлен без обновления статьи)
  - документы-потомки обновляются чаще, чем исходный документ

```
PUT blog
 {
   "mappings": {
     "article": {                 //Родитель
       "properties": {
         "title": {
           "type": "text"
         },
         "category": {
           "type": "keyword"
         }
       }
     },
     "comment": {                 //Ребенок
       "_parent": {
         "type": "article"
       },
       "properties": {
         "comment": {
           "type": "text"
         },
         "userid": {
           "type": "keyword"
         }
       }
     }
   }
 }
```

==добавление данных==
```
#Родитель
PUT blog/article/1 
{
  "title" : "Привет МеДвед!!!",
  "category" : "Сумбур да каламбур"
}

#Ребенок
PUT blog/comment/10?parent=1
{
  "comment" : "Продаю силиконовые сиськи",
  "userid" : "user1"
}
```

==получение данных,в elastic предоставляеть has_child/has_parent для запросов==
```
POST blog/article/_search
{
   "query": {
     "has_child": {
       "type": "comment",
       "query": {
         "term": {
           "userid": "user1"
         }
       }
     }
   }
 }
```

 документ потомок храниться в том же шарде что и родитель.

---
#### 🔹 Nested(вложение)

```json
{
 "id": 1,
 "name": "User1",
 "address": [                         //Nested
    {
      "street": "123 High Lane",
      "city": "Big City"
    },
    {
      "street" : "436 Low Lane",
      "city": "Small City"
    }
 ]
}
```

При обновлении адреса(или name) прийдеться обновлять весь документ, вложенные документы не могут быть доступны независимо от родительского документа так как они скрыты.

---
#### 🔹 ТИПЫ ДАННЫХ

- Core Data Types
	- Text Data Type (values are analyzed) - используеться для полнотекстового поиска
- Keyword Data Type (not analyzed,used for filter and aggregations) для структурирования данных (tags,categories,email,address ...), при поиске используется точное совпадение 
- Numeric Data Types (integer,float,byte ...)
    - long (64-битное целое число)
    - integer (32-битное целое число)
    - short (16-битное целое число)
    - byte (8-битное целое число)
    - double (вещественное число с двойной точностью)
    - float (вещественное число с ординарной точностью)
    - half_float (float с половинной точностью)
    - scaled_float (float но храниться как long,с помощью умножения на коэф. масштабирования)
- Date Data Type (string,integer)
- Boolean Data Type
- Binary Data Type (binary) - хранит закодированные в base64 строки(не индексируются, просто хранятся), используется для хранения изображений, сжатые большие обьекты ...
- Range Data Type (10-20) - {"gte": 10, "lte": 20}
- Complex Data Type (array,object)
- Nested Data Type (используется для вложеных обьектов)
```
{
   "properties": {
     "address_nested": {
       "type": "nested",
       "properties": {
         "street": {
           "type": "keyword"
         },
         "city": {
           "type": "keyword"
         }
       }
     }
   }
 }
```


- Geo Data Type
- Geo-point Data Type (долгота и широта)
- Geo-share Data Type (область)
- Specialized Data Type (ip)
---
#### 🔹 coerce
 автоматически данные корректными
 ```
"5" -> 5
"5.0" -> 5
5.0 -> 5 
 ```
---
#### 🔹 copy_to

 ==копирует значение в другое поле==
```
{
	"first_name": {
		"type": "text",
		"copy_to": "full_name"
	},
	"last_name": {
		"type": "text",
		"copy_to": "full_name"
	},
	"full_name": {
		"type": "text",
	}
}
```
---
#### 🔹 Example

```
POST search
{
    "settings": {
        "number_of_shards": 5,      //кол-во шардов
        "number_of_replicas": 2     //кол-во реприк
    },
    "mappings": {
        "hotels": {
            "dynamic" : "strict",   //будет вызвано исключение, если вы пытаетесь проиндексировать документ с неизвестным полем
            "_all": {               //отключаем сохранение всех полей в одном поле(уменьшаем индекс)
                "enabled": false
            },
            "properties": {
                "id" : {"type": "integer"},
                "resort": {
                    "type": "nested",
                    "properties": {
                        "id": {"integer"},
                        "name": {   //присваеваем два тип для поля
                            "type" : "text",
                            "analyzer": "russian"
                            "fields": {
                                "keyword": {
                                    "type": "keyword",
                                    "ignore_above": 256
                                }
                            }
                        },
                        "alias": {"type": "keyword"}
                    }
                },
                "country": {
                    "type": "nested",
                    "properties": {
                        "id": {"type": "integer"},                   //?
                        "name": {
                            "type" : "text",
                            "analyzer": "russian"
                            "fields": {
                                "keyword": {
                                    "type": "keyword",
                                    "ignore_above": 256
                                }
                            }
                        },
                        "alias": {"type": "keyword"},
                        "viza": {"type": "boolean"},
                        "cid": {"type": "keyword"}
                    }
                },
                "hotel": {
                    "type": "nested",
                    "properties": {
                        "id": {"type": "integer"}                   //?
                        "name": {
                            "type" : "text",
                            "fields": {
                                "keyword": {
                                    "type": "keyword",
                                    "ignore_above": 256
                                }
                            }
                        },
                        "alias": {"type": "keyword"},
                       "image": {                                   // ?
                            "type" : "string", 
                            "index" : "not_analyzed" 
                        },
                        "image_count": {"type": "integer"},
                        "geo_location": {"type": "geo_point"},
                        "rating": {"type": "float"},
                        "voter": {"type": "integer"},
                        "stars": {"type": "integer"},
                        "price": {"type": "float"},
                        "price_ua": {"type": "float"},
                        "services" {
                            "properies": {
                                "name": {"type": "keyword"}
                            }
                        }
                    }
                },
                "offers": {
                    "properties": {
                        "id": {"type": "integer"},
                        "operator_id": {"type": "integer"},
                        "tour_id": {"type": "integer"},
                        "included": {
                            "properties": {
                                "name": {"type": "keyword"}
                            }
                        },
                        "departure_city_id": {          // ?
                            "type": "integer"
                        },
                        "departure_date": {
                            "type": "date",
                            "format": "yyyy-MM-dd"
                        }
                        "accommodation": {"type": "keyword"},
                        "tourist": {
                            "type": "nested",
                            "properties": {
                                "adult": {"type": "integer"},
                                "child": {"type": "integer"},
                                "child_age": {
                                    "properties": {
                                        "age": {"type": "integer"}
                                    }
                                }
                            }
                        },
                        "days": {"type": "integer"},
                        "food": {"type": "keyword"},
                        "room": {
                            "type": "nested",
                            "properties": {
                                "id": {"type": "integer"}
                                "name": {"type": "keyword"}
                            }
                        },
                        "price": {
                            "type": "nested",
                            "properties": {
                                "price_currency": {"type": "float"},
                                "price_ua": {"type": "float"},
                                "currency": {"type": "keyword"},
                                "course": {"type": "float"}
                                "discount": {"type": "float"}
                            }
                        },
                        "transport": {"type": "keyword"},
                        "to": {
                            "type": "nested",
                            "properties": {
                                "from": {
                                    "type": "nested",
                                    "properties": {
                                        "code": {"type": "keyword"},
                                        "line": {"type": "text"},
                                        "port_to": {"type": "keyword"},
                                        "port_from": {"type": "keyword"},
                                        "place": {"type": "integer"},
                                        "begin": {
                                            "type": "date",
                                            "format": "yyyy-MM-dd HH:mm:ss"
                                        }
                                    }
                                },
                                "to": {
                                    "type": "nested",
                                    "properties": {
                                        "code": {"type": "keyword"},
                                        "line": {"type": "text"},
                                        "port_to": {"type": "keyword"},
                                        "port_from": {"type": "keyword"},
                                        "place": {"type": "integer"},
                                        "begin": {
                                            "type": "date",
                                            "format": "yyyy-MM-dd HH:mm:ss"
                                        }
                                    }
                                }
                            }
                        },
                        "stop_sale": {
                            "type": "nested",
                            "properties": {
                                "avia": {"type":"integer"},
                                "avia_back": {"type":"integer"},
                                "hotel": {"type":"integer"}
                            }
                        }
                    }
                }
            }
        }
    }
}
```
---